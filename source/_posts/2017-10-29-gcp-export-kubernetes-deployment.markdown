---
layout: post
title: "GCP export kubernetes deployment"
date: 2017-10-29 19:54:51 -0400
comments: true
categories: 
---

## GKE Custom Deployments

The Google Container Engine (GKE) + Google Compute Engine codelab (aside from the [codelab](https://codelabs.developers.google.com/codelabs/cloud-compute-kubernetes/index.html?index=..%2F..%2Findex#0) being in dire need of some updates, see this issue [here,](https://github.com/googlecodelabs/feedback/issues/243) [here,](https://github.com/googlecodelabs/feedback/issues/250) and [here](https://github.com/googlecodelabs/feedback/issues/274)) does not provide any introduction to kubernetes `deployments`.  K8s deployments are built using yaml files.  Customizing this yaml file allowed me to store secrets securely.

<!--more-->

This was exactly what I needed to do.  However I didn't have any idea how to write a deployment yaml file.. nor did I really have a desire to learn.  I already had a kubernetes cluster running in GKE with a container running my app code.

After googling around I found you could export a deployment as a yaml file.  This would allow you to customize and update the deployment as you progress without having to write the yaml file from scratch.

Here is the code that let me export my k8s deployment

```bash
kubectl get deployment myawesomedeployment -o yaml --export > k8s_deployment_config.yaml
```

This will now allow you to do neat stuff like add a secret to the yaml file along with, say updating an image in your deployment...

```bash
kubectl set image deployment/myawesomedeployment myawesomecontainer=gcr.io/myproject/myawesomecontainer:v2
```

Anyways this gave me a lot of flexibility and saved me a TON of time simply due to not having to learn how to write a deployment yaml file from scratch.  This is mostly just a post for helping me when I need to do this again, but maybe it will help someone else too!

My next post will explain how to deploy and interact with secrets in your Docker container through the kubernetes secrets object configuration.  [Google](https://cloud.google.com/sql/docs/postgres/connect-container-engine#5_create_your_secrets) has a basic introduction to the usage of secrets in your application in order to store and access database credentials.