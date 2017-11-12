---
layout: post
title: "how to cloud_sql_proxy pod"
date: 2017-11-01 22:54:58 -0400
comments: true
categories: 
---

So I don't have to google this every time..  **Make sure you stop your local postgres instance**.

Run this command to open a tunnel to the cloud sql instance in GCP.

```bash
cloud_sql_proxy -instances=<INSTANCE_NAME>=tcp:5432 -credential_file=<CRED_FILE> &
```

if the connection was successful you should now be able to tunnel into the remote cloud sql instance using...

```bash
psql "host=127.0.01 sslmode=disable dbname=<DB_NAME> user=<DB_USER> password=<DB_PASS>"
```