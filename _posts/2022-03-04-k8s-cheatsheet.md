---
categories: Kubernetes
title: Kubernetes Cheatsheet
date: 2022-03-04T00:00:00Z
author: Borys Generalov
---

[Kubernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/) is a powerful container orchestration engine.  It became one of the most favorite platforms due to its rich ecosystem, diverse instruments and many other benefits it offers. Kubernetes gives you features like service-discovery, fault-tolerance, inter-service communication, load-balancing, auto-scaling, etc out of the box. But Kubernetes is still incredibly complex; it takes time and patience to learn.

At first glance there is nothing hard about learning to use Kubernetes. You need to know fundamentals about [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/), such as control plane and worker nodes. Then you learn the most common resource types and how to use kubectl to perform different operations for Pods, Deployments, Services and a few other resources. That is the foundation that you can gain fairly quickly.

What makes it complicated is that you also need to learn all of the auxillary tools required to support the platform (Prometheus, Logging, Cluster management, Troubleshooting, Helm charts, CI/CD, GitOps, Infrastructure as a Code, service-meshes etc), without these you are flying blind. This is a huge learning curve, and it is not something that is easy to learn.

## Cheatsheet

Despite all the complexity, there are common routines that every developer wants to accomplish within Kubernetes. 
The Kubernetes command line tool `kubectl` is surprisingly approachable and relatively straightforward. I listed the most popular commands that will make your developer's life a little bit easier.

### List pods

Use the following command to retrieve a list of pods in given namespace:

```bash
kubectl get pods -n "my namespace"
```

To retrieve a list of pods in all namespaces:

```bash
kubectl get pods --all-namespaces
```


## Next steps

