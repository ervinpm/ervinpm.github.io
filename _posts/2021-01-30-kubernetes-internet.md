---
layout: post
title:  "No Internet in Kubernetes Pods"
date:   2021-01-30 00:00:00 -0600
categories: [kubernetes,archlinux]
---
After the painful setup of kubernetes in arch linux, the applications deployed in the pods are unable to access the internet and also talk to each other. I found this out when I installed Jenkins and was getting `UnknownHostException` errors and also unable to install plugins. There is an additional configuration that needs to be done in the flannel daemon.


### Testing if pods have Internet

For you to test if your `Jenkins` instance has internet you can `ssh` into it by using this command:

```bash
$ kubectl exec -i -t jenkins-pod-name -n jenkins -- /bin/bash
$ ping google.com
```

### Testing if pods can talk to each other

To test if your pods can talk to each other, you can run a `busy-box` instance and do a ping there. Just make sure that they are on the same namespace. In my case the namespace is `jenkins` Here is the command:

```bash
$ kubectl run -i --tty --rm debug --image=busybox -n jenkins --restart=Never -- sh
```

### Updating Flannel Daemon

Apparently these problems are all caused by Flannel not using the correct `iface`. A noob like me still does not know what that means. Nonetheless here is what I did.

1. Get the network interface name of your network. Mine is `eno1`:
```bash
$ ip -4 addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    altname enp0s25
    inet 192.168.1.14/24 brd 192.168.1.255 scope global dynamic noprefixroute eno1
       valid_lft 82467sec preferred_lft 71667sec
3: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default
    inet 10.244.0.0/32 brd 10.244.0.0 scope global flannel.1
       valid_lft forever preferred_lft forever
4: cni0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    inet 10.244.0.1/24 brd 10.244.0.255 scope global cni0
       valid_lft forever preferred_lft forever
```

2. Update the flannel daemon configuration. The `KUBE_EDITOR=vim` is optional, I just did not like the default `vi` editor.
```bash
$ KUBE_EDITOR=vim kubectl edit DaemonSet kube-flannel-ds -n kube-system
```

Set the value to the name of the network interface you got in step 1. Here is how mine looks like.

```yaml
      containers:
      - args:
        - --ip-masq
        - --kube-subnet-mgr
        - --iface=eno1
```

**That's it!** these two steps solved this problem. 

### References

https://medium.com/@anilkreddyr/kubernetes-with-flannel-understanding-the-networking-part-1-7e1fe51820e4

https://www.mankier.com/1/kubectl-edit

https://www.jeffgeerling.com/blog/2019/debugging-networking-issues-multi-node-kubernetes-on-virtualbox