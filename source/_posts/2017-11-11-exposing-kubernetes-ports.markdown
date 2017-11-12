---
layout: post
title: "exposing kubernetes ports"
date: 2017-11-11 17:45:04 -0500
comments: true
categories: 
---

## LoadBalancers on Kubernetes

`kubectl expose deployment categorylinksbot --port=80 --target-port=5000 --type=LoadBalancer`

Make sure you specify the port the load balancer should listen on, as well as the "target port" to forward traffic from the load balancer to.  `--target-port` is the port your web app is listening on for requests (flask web apps default to 5000 I think).  `--port` is the port you wish the load balancer to receive traffic on.
