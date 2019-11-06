class: middle, center
# Advanced Interactions with Kubernetes
### As Taught by Helm
#### Taylor Thomas 
##### Senior Software Engineer at Microsoft Azure

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
- Properly using and retrieving the correct statuses of objects
- 3 way patching
- Object building (including unstructured types and how to convert them)
- Validation
- Discovery client and cache invalidation
- A brief review of the more obtuse/little-known k8s packages


???

- We may not have time to go over all of the points here, but I will be breezing
  along fairly rapidly. There is a link to the slides at the end so you can look
  over them at your lesiure later
- I plan to stop briefly for questions at the end of each topic so you don't
  have to hold on to them as we go through the rest of the topics
