class: middle, center
# Advanced Interactions with Kubernetes
### As Taught by Helm
##### Taylor Thomas - Senior Software Engineer at Microsoft Azure

.footnote[Cloud Native Rejekts San Diego 2019]

---
# Who am I?

- Senior Software Engineer in Microsoft Azure
- Helm Core Maintainer
- GitHub: [@thomastaylor312](https://github.com/thomastaylor312)
- Twitter: [@_oftaylor](https://twitter.com/_oftaylor)
- Kubernetes Slack: @oftaylor

???

- I'm a software engineer at Azure primarily working on Helm, but also some
  other open source projects. I used to work on AKS as well
- So why listen to me?
- Been a core maintainer on Helm for 3 years
- Done waaaaay too much work interacting with Kubernetes (going back to version
  1.2)
- One more final note, these will be pretty advanced topics that assume you
  already know about different Kubernetes objects and some of the underlying
  details of how it deals with different situations (patching, API requests,
  etc.). So if you do not have that knowledge, you are more than welcome to
  stay, but it might get a bit confusing. And even though we are using Helm as a
  backdrop for all of this, no prior knowledge of Helm or its code is required

---

# Topics

- Handling multiple API versions
- Using object statuses
- 3 way patching
- Object building (including unstructured types and how to convert them)
- Validation
- Discovery client and cache
- A brief review of the more obtuse/little-known k8s packages

???

- We may not have time to go over all of the points here, but I will be breezing
  along fairly rapidly. There is a link to the slides at the end so you can look
  over them at your lesiure later
- I plan to stop briefly for questions at the end of each topic so you don't
  have to hold on to them as we go through the rest of the topics

---

# Multiple API Versions

- This is probably much more simple than you think it would be
--
count: false

- The answer? Use the most recent version of the API

???

- This applies to both built in k8s types and CRDs (k8s does conversion
  underneath the hood)

--
count: false
```go
switch value := AsVersioned(v).(type) {
case *appsv1.Deployment, *appsv1beta1.Deployment, *appsv1beta2.Deployment, *extensionsv1beta1.Deployment:
    currentDeployment, err := w.c.AppsV1().Deployments(v.Namespace).Get(v.Name, metav1.GetOptions{})
    if err != nil {
        return false, err
    }
...
}
```

???

- However, you can also do cool things like this with a raw `runtime.Object`

---
layout: true
# Using object statuses
#### Some examples from `--wait`

---

- Be warned: Statuses are a bit of a hairy mess
- Always check the [Kubernetes API Reference]() for specific definitions on all
  status fields

???

- They can be a bit messy, but that is why I have some examples for you, from
  Helm's wait logic
- There could be various things you want to do with status objects, so I am
  going to focus on what the fields in each status indicate and things to watch
  out for.
- I'll be starting easy, and then progressing to harder ones with each example

---

##### Deployments

```go
// If paused deployment will never be ready
if currentDeployment.Spec.Paused {
    continue
}
// Find RS associated with deployment
newReplicaSet, err := deploymentutil.GetNewReplicaSet(currentDeployment, w.c.AppsV1())
if err != nil || newReplicaSet == nil {
    return false, err
}
```

???

- This is the code that actually gets the most current replica set and checks
  for a few other things
- One of the important things to note is that often you have to check both the
  Spec and the Status to ascertain the true status of the object

---

##### Deployments

```go
func (w *waiter) deploymentReady(rs *appsv1.ReplicaSet, dep *appsv1.Deployment) bool {
	expectedReady := *dep.Spec.Replicas - deploymentutil.MaxUnavailable(*dep)
	if !(rs.Status.ReadyReplicas >= expectedReady) {
		w.log("Deployment is not ready: %s/%s. %d out of %d expected pods are ready", dep.Namespace, dep.Name, rs.Status.ReadyReplicas, expectedReady)
		return false
	}
	return true
}
```

- You can tell if a `Deployment` is ready by: number of `ReadyReplicas` in the new `ReplicaSet` == The number you want

???

- The code is the actual check and is fairly simple. I'll cover what is
  in deployment util later on
- In a classic case of "who wrote this code?! Oh, it's me," I can't remember why
  we don't use a combination of the `unavailableReplicas` and `readyReplicas`
  fields, but I think there is a reason about what is getting updated. Basically
  this may end up with me re-writing this code block

---

##### DaemonSets

```go
// Make sure all the updated pods have been scheduled
if ds.Status.UpdatedNumberScheduled != ds.Status.DesiredNumberScheduled {
    w.log("DaemonSet is not ready: %s/%s. %d out of %d expected pods have been scheduled", ds.Namespace, ds.Name, ds.Status.UpdatedNumberScheduled, ds.Status.DesiredNumberScheduled)
    return false
}
```

???

- This is a super interesting (and I feel helpful) tidbit. These two fields in
  the status help tell you if the updated spec is scheduled on all nodes.
  Because the number of scheduled nodes can differ, these two are all you need
  to reconstruct the current scheduling status of a DaemonSet (which sometimes
  takes a bit, given that many heavy admin processes in a cluster use a DS)

---

##### DaemonSets

```go
maxUnavailable, err := intstr.GetValueFromIntOrPercent(ds.Spec.UpdateStrategy.RollingUpdate.MaxUnavailable, int(ds.Status.DesiredNumberScheduled), true)
if err != nil {
    // If for some reason the value is invalid, set max unavailable to the
    // number of desired replicas. This is the same behavior as the
    // `MaxUnavailable` function in deploymentutil
    maxUnavailable = int(ds.Status.DesiredNumberScheduled)
}

expectedReady := int(ds.Status.DesiredNumberScheduled) - maxUnavailable
if !(int(ds.Status.NumberReady) >= expectedReady) {
    w.log("DaemonSet is not ready: %s/%s. %d out of %d expected pods are ready", ds.Namespace, ds.Name, ds.Status.NumberReady, expectedReady)
    return false
}

```
- You can tell if a `DaemonSet` is ready by: `UpdatedNumberScheduled` ==
  `DesiredNumberScheduled` && `NumberReady` == The number you want

???

- Here you'll see the use of some of the same fields we used before. Note again
  the need to reach into the spec if you want to introspect some more
- We also use an interesting k8s util called `intstr`, which I'll go more into
  later
- The other major field here is `NumberReady`, which shows the actual number of
  running pods. However, this number also includes pods with the old spec that
  are still running and ready
- Like I said before, the summary here is that you need to put a bunch of the
  information together from an object status to know programmatically the
  current state

---

##### StatefulSets

The intricacies of the `partition`

```go
var partition int
// 1 is the default for replicas if not set
var replicas = 1
// For some reason, even if the update strategy is a rolling update, the
// actual rollingUpdate field can be nil. If it is, we can safely assume
// there is no partition value
if sts.Spec.UpdateStrategy.RollingUpdate != nil && sts.Spec.UpdateStrategy.RollingUpdate.Partition != nil {
    partition = int(*sts.Spec.UpdateStrategy.RollingUpdate.Partition)
}
if sts.Spec.Replicas != nil {
    replicas = int(*sts.Spec.Replicas)
}
```

???

- Next up on the fun status things that can trip you up, let's talk about
  `StatefulSets` and partitioning. Partitioning is used for times when you want
  to roll out a canary to one or two nodes before updating the rest
- First thing to know about  is that SO MANY fields are optional, so know that
  the default expected replicas is 1 and the default partition is 0
- Also, note the comment I have on that gnarly `if` statement. You can have a
  null update strategy, even though all of the other objects always have one set

---

##### StatefulSets

```go
expectedReplicas := replicas - partition

// Make sure all the updated pods have been scheduled
if int(sts.Status.UpdatedReplicas) != expectedReplicas {
    w.log("StatefulSet is not ready: %s/%s. %d out of %d expected pods have been scheduled", sts.Namespace, sts.Name, sts.Status.UpdatedReplicas, expectedReplicas)
    return false
}

if int(sts.Status.ReadyReplicas) != replicas {
    w.log("StatefulSet is not ready: %s/%s. %d out of %d expected pods are ready", sts.Namespace, sts.Name, sts.Status.ReadyReplicas, replicas)
    return false
}
```

- You can tell if a `StatefulSet` is ready by: `UpdatedReplicas` == `replicas` -
  `partition` && `ReadyReplicas` == `replicas`

???

- Similar to `DaemonSets`, you need to first check if all of the pods are
  scheduled, because the `ReadyReplicas` number includes previous specs that are
  running
- However, even if you are partitioning, you should expect all of the old pods
  _and_ the new pods to be ready and equal to the number of desired replicas

---
layout: false
# Using object statuses
#### Gotchas, caveats, provisos

- Always validate your assumptions

???

- In summary, there are a lot of gotchas and things to be aware of for using
  status object. Each object behaves differently and the numbers can each mean
  different things even if they are named the same. 
- Because of the extensible nature of the kubernetes API and the seemingly
  infinite knobs you can tweak, make sure you don't assume that a status is
  giving you the correct info (ask me how I know)

--
count: false
- Ingress status does not work how you think. To quote
  [specifically](https://kubernetes.slack.com/archives/C09QYUH5W/p1572019798187300):

???

- One big heads up I recently learned is how the ingress status works

--
count: false
> no [the status] is not required. It would make it also complicated if you have
> one controller that creates the L4 LB and another controller that does the L7
> routing. In some controllers you can have L4 and L7 in one, such that you have
> the optional flag.

--
count: false
- Last but not least: Remember Kubernetes uses eventual consistency

???

- This means that the status does not always perfectly reflect reality. Although
  it does 98% of the time

---

# 3-way patches

???

This is simpler than you might think

---

# Object Building
#### Unstructured vs. Structured

???

TODO: Make sure to note schema registration

---

# Object Building
#### Conversion

---

# Validation

???

TODO: Figure out if a CRD validation example is needed

---

# Discovery Client and Cache
#### Under the hood details

???

TODO: Review the various parts of the code and what it is doing to cache things

---

# Discovery Client and Cache
#### Cache invalidation

???

TODO: Answer why you'd want to invalidate the discovery cache

---
layout: true

## Little Known Kubernetes Packages

---

#### k8s.io/kubernetes/deployment/util

???

TODO: Show the Helm code we copied over

---

#### sigs.k8s.io/yaml

---

#### k8s.io/cli-runtime/pkg/resource

???

TODO: Talk about the flexibility of this package

---

#### k8s.io/cli-runtime/pkg/genericclioptions

---

### k8s.io/apimachinery/pkg/util/intstr

???
- This is a handy little utility for converting all of those fields that can be
  an int value or a string in YAML

---

#### TODO: Maybe scheme registration?

---

layout: false
class: middle, center
# Thank You

### [https://slides.oftaylor.com/AdvancedKubernetesInteractions/](https://slides.oftaylor.com/AdvancedKubernetesInteractions/)
