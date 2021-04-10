---
layout: post
title:  "Setting up Docker Registry in Kubernetes"
date:   2021-03-08 00:00:00 -0600
categories: [coding,docker,kubernetes,linux]
---
If you want to deploy custom docker images in your private kubernetes cluster, you'll also want a private image repository.

## Create a persistent volume claim
This is where your images will live. Here, I am using a dynamic nfs provisioner. More info found [here]({% post_url 2021-02-15-kubernetes-dynamic-nfs %})
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: registry-claim
spec:
  storageClassName: managed-nfs-storage
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

## Create registry deployment configuration
This is the actual registry server
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: registry-deploy 
  labels:
    app: docker-registry 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: docker-registry
  template:
    metadata:
      labels:
        app: docker-registry
    spec:
      volumes:
      - name: registry-volume
        persistentVolumeClaim:
          claimName: registry-claim
      containers:
        - image: registry:2
          name: docker-registry
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5000
          volumeMounts:
          - name: registry-volume
            mountPath: /var/lib/registry
```

## Expose as a service

You can choose to expose this as a NodePort and just use the generated port and node name. I wanted to take it a step further and expose it as a secured registry.

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: docker-registry
  name: registry-service
spec:
  ports:
  - port: 5000
    protocol: TCP
    targetPort: 5000
  selector:
    app: docker-registry
  type: ClusterIP

```

## Create `htpasswd` Secret

To secure your registry, basic authentication can be used by creating an htpasswd file. Install apache2-utils first if you don't have it yet

```bash
$ sudo apt install apache2-utils
```

Generate the htpasswd file.

```bash
$ htpasswd -c auth username password
```

Create a kubernetes secret

```bash
$ kubectl create secret generic registry-auth --from-file=auth
```

## Certicate Generation 

By default, docker clients require you to connect to a secure registry (https). You'll be needing a certificate. I'll be using Let's Encrypt ACME. More information about that available [here]({% post_url 2021-02-15-kubernetes-dynamic-nfs %})

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: registry-certificate
spec:
  dnsNames:
    - registry.myawesomesite.com
  secretName: registry-tls
  issuerRef:
    name: letsencrypt
    kind: ClusterIssuer

```

## Ingress
Time to combine things together

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: registry-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: registry-auth
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required'
    nginx.ingress.kubernetes.io/proxy-body-size: "0"    
spec:
  tls:
  - hosts:
      - registry.myawesomesite.com
    secretName: registry-tls
  rules:
  - host: registry.myawesomesite.com
    http:
      paths:
      - path: "/"
        pathType: Prefix
        backend:
          service:
            name: registry-service
            port:
              number: 5000

```

## Test

If everything went well, you can easily test this by going directly to the api using a browser
```
https://registry.\<myawesomesite\>.com/v2/_catalog
```

You should see an empty json object
```json
{}
```

Now to test if you can actually push private images into your registry. Try logging in to your registry

```bash
$ docker login
Username: <myusername>
Password:
Login Succeeded
```

After successful login, pull an image from dockerhub, tag it then push it to your repo

```bash
$ docker pull nginx
$ docker tag nginx:latest myregistryurl/nginx:1.19
$ docker push myregistryurl/nginx:1.19
```

Go back to your browser and refresh the catalog and see if the image got pushed.

## Using this in kubernetes

Create registry credential secret

```bash
$ kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email> 
``` 

Update your service account to use the registry credential

```bash
$ kubectl patch serviceaccount default \
  -p "{\"imagePullSecrets\": [{\"name\": \"regcred\"}]}" \
  -n <your-namespace>
```