---
layout: post
title:  "Dealing With Ingress Path Problems"
date:   2021-04-12 00:00:00 -0600
categories: [coding,linux,kubernetes]
---

Because I am a noob at kubernetes, I have been having problems deploying applications and creating ingress paths. I only wanted a single doman then different paths like `apps.myk8s.com/app1`, `apps.myk8s.com/app2` so on and so forth...

Everytime I create an ingress an ingress rule, I get a lot of issues with the html components (i.e. css, images and js) and getting 404s. To resolve this, I just needed to add a couple of annotations:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app1
  labels:
    app: app1
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/configuration-snippet: |
      rewrite ^(/app1)$ $1/ permanent;
spec:
  rules:
  - host: apps.myk8s.com
    http:
      paths:
      - path: /app1(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: app1
            port:
              number: 8080

```

The first annotation tells ingress to pass the parameters to the service without the sub folder. The second annotaton tells ingress to append `/` at the end of the address if it is missing. This resolves the resources not being relative to the subfolder. 

The path has 2 regex groups that detect the presence of `/` and parameters that is used by the annotations.
