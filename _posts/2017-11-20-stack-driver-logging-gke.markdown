---
layout: post
title: "StackDriver logging and Monitoring with GKE"
date: 2017-11-20 13:20:25 -0500
comments: true
categories: 
---

Google Container Engine (GKE) recently (like in the past 48 hours) changed it's name to Google Kubernetes Engine, which just makes more sense.  Anyways, I needed to enable monitoring and logging on my GKE cluster as I was (drumroll) hacked again!!! 🎉🎉🎉 yay!!! So I really needed monitoring and better logging enabled.  When you spin up a GKE cluster, the VM's that google provisions for you on GCE are using their proprietary node image Container-Optimized OS (cos).  "cos" is a stripped down GNU/Linux image.  Unfortunately the install [script](https://cloud.google.com/logging/docs/agent/installation) Google provides will not run on this because in the install [script](https://dl.google.com/cloudagents/install-logging-agent.sh) the below piece of code cannot identify the cos image.

<!--more-->

```bash
install() {
  case "${ID:-}" in
    amzn)
      echo 'Installing agents for Amazon Linux.'
      install_for_amazon_linux
      ;;
    debian|ubuntu)
      echo 'Installing agents for Debian or Ubuntu.'
      install_for_debian
      ;;
    rhel|centos)
      echo 'Installing agents for RHEL or CentOS.'
      install_for_redhat
      ;;
    *)
      # Fallback for systems lacking /etc/os-release.
      if [[ -f /etc/debian_version ]]; then
        echo 'Installing agents for Debian.'
        install_for_debian
      elif [[ -f /etc/redhat-release ]]; then
        echo 'Installing agents for Red Hat.'
        install_for_redhat
      else
        echo >&2 'Unidentifiable or unsupported platform.'
        exit 1
      fi
  esac
}
```

To get stackdriver running you have to modify the node image for GKE to Ubuntu..

## HOWEVER

There is also this [monitoring tutorial](https://cloud.google.com/kubernetes-engine/docs/monitoring) and a [logging tutorial](https://cloud.google.com/kubernetes-engine/docs/logging) specifically for kubernetes engine, which I didn't find until just now.  Maybe I'm an idiot.

Just gonna reproduce everything I did today here for clarity..

```bash
gcloud beta container clusters update my-awesome-cluster --monitoring-service=monitoring.googleapis.com
```
and

```bash
gcloud beta container clusters update my-awesome-cluster --logging-service=logging.googleapis.com
```

I could also get Prometheus running as a pod in my cluster.. so that might be cool too.  I may try this at some point.  Prometheus may be overkill for what I'm doing right now.
