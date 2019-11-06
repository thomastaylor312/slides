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

---

# Using object statuses
#### Some examples from `--wait`

---

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

## Little Known Kubernetes Packages
#### k8s.io/kubernetes/deployment/util

???

TODO: Show the Helm code we copied over

---

## Little Known Kubernetes Packages
#### sigs.k8s.io/yaml

---

## Little Known Kubernetes Packages
#### k8s.io/cli-runtime/pkg/resource

???

TODO: Talk about the flexibility of this package

---

## Little Known Kubernetes Packages
#### k8s.io/cli-runtime/pkg/genericclioptions

---

class: middle, center
# Thank You

### [https://slides.oftaylor.com/AdvancedKubernetesInteractions/](https://slides.oftaylor.com/AdvancedKubernetesInteractions/)
