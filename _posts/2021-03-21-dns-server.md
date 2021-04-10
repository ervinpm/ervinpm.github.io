---
layout: post
title:  "Deploy Angular App to Kubernetes"
date:   2021-03-19 00:00:00 -0600
categories: [coding,docker,angular,linux,nginx]
---
To deploy an angular app to kubernetes, you'll have to containerize you application. In this example, I am going to use docker.


## Create a docker file

Place this in the root folder of your angular poroject

```Dockerfile
FROM nginx:1.17.1-alpine
COPY nginx.conf /etc/nginx/nginx.conf
COPY /dist/myawesome-app /usr/share/nginx/html
```

## Create nginx config file

Place this in the root folder of your angular project

```conf
events{}

http {

    include /etc/nginx/mime.types;

    server {

        listen 8080;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;

        location / {
                try_files $uri $uri/ /index.html;
        }
    }
}
```

## Build your angular project

If you are deploying your project in a subfolder like i am, you'll have to specify the base href value. Note: I had to execute this in windows `cmd.exe` because of a weird bug in `ng cli` where the folder absolute path was being appended.

```shell
$ ng build --prod --base-href="/myawesome-app/"
```

## Build the container image

In the example below, I am using a private registry, you can omit the registry address if you are using docker hub. The period `.` at the end is intentional. 

```shell
$ docker build -t registry.example.com/myawesome-app:latest .
```

## Push the image to your registry

```shell
$ docker push registry.example.com/myawesome-app:latest
```

## Create a deploy configuration in Kubernetes

In the deploy configuration, make sure to set `imagePullPolicy` to `Always` so it can pickup changes right away

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myawesome-app
  labels:
    app: myawesome-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myawesome-app
  template:
    metadata:
      labels:
        app: myawesome-app
    spec:
      containers:
        - image: registry.example.com/myawesome-app
          name: myawesome-app
          imagePullPolicy: Always
          ports:
            - containerPort: 8080
```

## Create a service

Expose your app using a service, you may choose to create a `NodeIP` but I'll be creating a `ClusterIP` since I'll be using an ingress configuration.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myawesome-app
  labels:
    app: myawesome-app
spec:
  type: ClusterIP
  selector:
    app: myawesome-app
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP

```

## Create ingress rule

If you are going to use a sub directory in your host, the you'll have to add a regex path: `(/|$)(.*)` 

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myawesome-app
  labels:
    app: myawesome-app
spec:
  rules:
  - host: www.mydomain.com
    http:
      paths:
      - path: /myawesome-app(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: myawesome-app
            port:
              number: 8080
```