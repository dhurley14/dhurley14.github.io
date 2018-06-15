---
layout: post
title: "Copying K8s pod data"
date: 2018-06-15 14:47:11 -0400
comments: true
categories: 
---

I'm toying with the idea of moving my app off of GCP and onto Digital Ocean (trying to reduce costs). My user tokens are saved in redis along with other state data. So naturally I need to move the backup file created by redis from GCP to digital ocean. Luckily kubernetes comes with this handy tool built into `kubectl` that will copy any data from the mounted directory of a pod. Pretty sweet.

```bash
kubectl cp  redis-0:dump.rdb ~/myredis/.
```
