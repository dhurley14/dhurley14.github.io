---
layout: post
title: "GCP export kubernetes deployment"
date: 2017-10-29 19:54:51 -0400
comments: true
categories: 
---

## GKE Custom Deployments

The Google Container Engine (GKE) + Google Compute Engine codelab (aside from the [codelab](https://codelabs.developers.google.com/codelabs/cloud-compute-kubernetes/index.html?index=..%2F..%2Findex#0) being in dire need of some updates, see this issue [here,](https://github.com/googlecodelabs/feedback/issues/243) [here,](https://github.com/googlecodelabs/feedback/issues/250) and [here](https://github.com/googlecodelabs/feedback/issues/274)) does not provide any introduction to kubernetes `deployments`. K8s deployments are built using yaml files. Manually customizing this yaml file allowed me to more easily manage secrets securely, among other benefits.

<!--more-->

When you start going through tutorials on how to deploy and manage your containers with kubernetes you end up running a bunch of `kubectl` (cube-control?) commands. These all basically map to updating text in a yaml file. Sometimes it would be easier to just write the necessary yaml out by hand. In order to do this you need some place to start. Luckily `kubectl` allows you to export all of the commands you've run against your k8s deployment into a file. This was exactly what I needed. I already had a kubernetes cluster running in GKE with a container running my app code. Now I needed to set up the necessary secrets and add another container in my pod so that I could access my Postgres Cloud SQL instance.

Here is the code that let me export my k8s deployment.

```bash
kubectl get deployment myawesomedeployment -o yaml --export > k8s_deployment_config.yaml
```

The `kubectl` docs have a more thorough listing of all [available options](https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get) for exporting deployments, among other things.

You don't necessarily need a deployment configuration file to, say, update one of the containers running in a pod. However if you want to quickly add another container to your pod, along with some other settings (like a SECRET!!!...) without running seven different `kubectl` commands (and trying to remember them all for future deployments), a deployment configuration file will help you swiftly accomplish this.


This is mostly just a post for helping me when I need to remember how to do this again, but maybe it will help someone else too!

My next post will explain how to deploy and interact with secrets in your Docker container through the kubernetes secrets object configuration. Google has a [basic introduction](https://cloud.google.com/sql/docs/postgres/connect-container-engine#5_create_your_secrets) to the usage of secrets in your application in order to store and access database credentials.