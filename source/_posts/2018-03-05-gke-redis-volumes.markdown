---
layout: post
title: "GKE and Redis"
date: 2018-03-05 22:41:46 -0500
comments: true
categories: 
---

Redis allows users to save data to a single file with the [bgsave](https://redis.io/commands/bgsave) command.  If left unspecified inside your `redis.conf` file, bgsave will write this data to /docker/host/dir:/data.

I had always assumed you needed to specifically mount a volume for this to work, however it apparently works even when no volume is specified, thus allowing easier deployment scaling in kubernetes.
