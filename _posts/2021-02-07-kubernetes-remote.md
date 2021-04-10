---
layout: post
title:  "Use kubectl for remote cluster"
date:   2021-02-07 00:00:00 -0600
categories: [coding,kubernetes]
---
## Kubectl Installation
After installing Kubernetes on Bare Metal, you might want to do `kubectl` commands on a remote machine. To do that, you'll have to install `kubectl` on your machine. Installation is found here:

https://kubernetes.io/docs/tasks/tools/install-kubectl/


## Configuration

After instaling kubectl, how do you tell how to connect to your cluster? To do that, you'll have to go to the kubernetes master node and copy the config file. This is found by default in: `~.kube/config`. Copy that file or the contents and create a similar structured folder. In windows, it looks something like this:

```
C:\Users\username\.kube\config
```