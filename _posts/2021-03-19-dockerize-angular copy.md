---
layout: post
title:  "Setting up Bind DNS Server in Kubernetes"
date:   2021-03-21 00:00:00 -0600
categories: [coding,docker,linux,kubernetes,dns]
---

If you are tired of typing ip addresses to access your home computers, you'll need a private DNS server so you can just adress them as `myservername`.

In this example, I will be using a kubernetes cluster that I already have setup. This also allows you to use it as a DNS forwarder

## Deplyment configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bind-deployment
  labels:
    app: bind
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bind
  template:
    metadata:
      labels:
        app: bind
    spec:
      containers:
      - name: bind
        image: cytopia/bind
        env:
        - name: ALLOW_QUERY
          value: 192.168.1.0/24,127.0.0.1,localnets
        - name: EXTRA_HOSTS
          value: myserver01=192.168.1.101,myserver02=192.168.1.102,myserver03=192.168.1.103,myserver04=192.168.1.104
        - name: DNS_FORWARDER
          value: 8.8.8.8
        - name: ALLOW_RECURSION
          value: 192.168.1.0/24,127.0.0.1,localnets
        ports:
        - containerPort: 53
```

## Service Configuration

I used `MetalLB` for load balancing configuration. This will allow you to point a router or a computer to the DNS server with default port (`53`).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: bind-service
spec:
  type: LoadBalancer
  selector:
    app: bind
  ports:
  - port: 53
    targetPort: 53
    protocol: UDP

```

## Router Configuration

For this to work, you either setup each of your computers or the router. By default, the router will use DNS of your Internet Service Provider (ISP). I changed this to point to the IP assigned by `MetalLB` for the DNS server.