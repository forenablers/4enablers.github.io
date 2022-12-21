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

Despite all the complexity, there are common routines that every developer wants to accomplish within Kubernetes. Similarly, there are common approaches and tooling, such as `kubectl` and `Helm`. These command line tools are surprisingly approachable and straightforward. Below I listed the most popular commands/tasks that will make your daily developer's duties a little bit easier.

### Switch context

A context is a configuration that you use to connect to a certain cluster. For example, you can connect to a `minikube` cluster running your machine (local dev environment) or connect to a continuos integration cluster running on a remote machine (test environment). 

The following command can be used to view all the contexts:

```bash
kubectl config get-contexts
```

View current context:

```bash
kubectl config current-context
```

Switch to the desired context:

```bash
kubectl config use-context "arn:aws:eks:<aws-region>:<id>:cluster/test-eks"
```

### List pods

Use the following command to retrieve a list of pods in given namespace:

```bash
kubectl get pods -n "<namespace>"
```

To retrieve a list of pods in all namespaces:

```bash
kubectl get pods --all-namespaces
```

### Get pod logs

Get the logs for the last 15 minutes for a pod:

```bash
kubectl logs --since=15m "<pod_name>"
```
### Get resource usage

Note that the following command requires the Metrics API installed.

Display resource usage (CPU/RAM) of pdos per container:

```bash
kubectl top pods -n "<namespace>" --containers
```

Get metrics for the node:

```bash
kubectl top node 
```

### Describe services

Display the detailed information about the service in the given namespace:

```bash
kubectl describe services "<service_name>" -n "<namespace>"
```

Extract the IP address of the service:

```powershell
$ipAddress = (kubectl describe services "<service_name>" -n "<namespace>" | Select-String "IP" |)
Write-Host $ipAddress
```

### Secrets

Create a ssl certificate secret in the given namespace:

```bash
kubectl create secret tls "<secret_name>" -n "<namespace>" --key ./key.pem --cert ./certificate.crt
```

View the secrets:

```bash
kubectl get "secrets/<secret_name>" -n "<namespace>"
```

### Helm charts

To list all the Helm chart dependencies:

```bash
helm dependency list "<chart_name>"
```

To upgrade the release with all dependencies, run the command as follows:

```bash
helm upgrade "<chart_name>" -i "<release_name>" -n "<namespace>" --set tags.all=enabled
```

To upgrade the release without certain dependencies, for example exclude sub-charts tagged as frontend, run the command as follows:

```bash
helm upgrade "<chart_name>" -i "<release_name>" -n "<namespace>" --set tags.frontend=false

```

To get the values for a named release:

```bash
helm get values ringbax -n "<namespace>"
```

To list all installed charts:

```bash
helm ls -n "<namespace>"
``` 

### Redeploy pods

To shut down and restart each container in your deployment one by one, run this command:

```bash
kubectl rollout restart deploy -n "<namespace>"
```

To remove all the pods in a given namespace:

```bash	
kubectl delete pod --all -n "<namespace>"
```

## Next steps

Consider this list a starting point for your Kubernetes cheatsheet. These commands are simple, and cover typical operations, hence are good to have for any developer working with Kubernetes.

* Learn more about [Kubernetes](https://kubernetes.io/docs/home/)
* Learn about [Helm](https://helm.sh/) package manager for Kubernetes
* [minikube](https://github.com/kubernetes/minikube) for local development
* [k9s](https://k9scli.io/) command line interface for an overview of your cluster resources
