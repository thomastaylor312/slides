class: middle, center
# Advanced Interactions with Kubernetes
### As Taught by Helm
##### Taylor Thomas - Senior Software Engineer at Microsoft Azure

.footnote[Cloud Native Rejekts San Diego 2019]

???

- I'm a software engineer at Azure primarily working on Helm, but also some
  other open source projects
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
  going to focus on what the fields in each status indicate

---

##### Deployments

```go
// If paused deployment will never be ready
*if currentDeployment.Spec.Paused {
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

???

- The code is the actual check and is fairly simple. I'll cover what is
  in deployment util later on
- In a classic case of "who wrote this code?! Oh, it's me," I can't remember why
  we don't use a combination of the `unavailableReplicas` and `readyReplicas`
  fields, but I think there is a reason about what is getting updated. Basically
  this may end up with me re-writing this code block

---

##### DaemonSets

---

##### StatefulSets

---

##### Services

---
layout: false
# Using object statuses
#### Gotchas, caveats, provisos

???

TODO: Talk about how ingress status doesn't work like you think

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

#### TODO: Maybe scheme registration?

---

layout: false
class: middle, center
# Thank You

### [https://slides.oftaylor.com/AdvancedKubernetesInteractions/](https://slides.oftaylor.com/AdvancedKubernetesInteractions/)
