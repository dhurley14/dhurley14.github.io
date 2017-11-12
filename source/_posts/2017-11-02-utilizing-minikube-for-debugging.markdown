---
layout: post
title: "utilizing minikube for debugging"
date: 2017-11-02 23:29:33 -0400
comments: true
categories: 
---


1. `minikube start`
2. from the same terminal window you ran minikube start run `eval $(minikube docker-env)`
3. again, from the same terminal window, run your docker build command
4. update your deployment spec's* pod to point to the local docker image, whatever you tagged it as.
5. update your deployment spec's imagePullPolicy to be Never (`imagePullPolicy: Never`)
6. kubectl create -f deployment_spec.yaml
7. wait for the pods to get created (should not take long)
8. expose a loadbalancing service via `kubectl expose deployment mydeployment --type="LoadBalancer"`
9. see below..

    ```bash
kubectl get services
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
mydeployment   LoadBalancer   10.0.0.40    <pending>     5000:32711/TCP   5s
kubernetes         ClusterIP      10.0.0.1     <none>        443/TCP          13m
    ```
10. Generally, cloud providers will then provide an ip address for access to the service.  However since this is minikube there isn't anyone assinging IP's to anything.  Minikube allows you to simulate this by running `minikube service <load balancer from step 9>` and it will attempt to open a web page to your service.
11. [These go to 11.](https://www.youtube.com/watch?v=KOO5S4vxi0o)


\* get your deployment spec from remote [see here](http://dhurley14.github.io/blog/2017/10/29/gcp-export-kubernetes-deployment/)