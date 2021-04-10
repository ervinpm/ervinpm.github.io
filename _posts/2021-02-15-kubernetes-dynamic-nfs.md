---
layout: post
title:  "Installing Dynamic NFS Persistent Volume in Kubernetes"
date:   2021-02-15 00:00:00 -0600
categories: [coding,kubernetes]
---
## Prepare a NFS 
You will need the `IP` address of the NFS server and the folder path.

## Clone the NFS Provisioner
NFS Provisioner `YAML` files are found in Kubernetes SIGS repository. This assumes that you already have git installed

```bash
$ git clone git@github.com:kubernetes-sigs/nfs-subdir-external-provisioner.git
```

## Update NFS Details
After cloning the repository, udpate `deployment.yaml` and change the following to the nfs details that you have noted earlier

```yaml
          env:
            - name: PROVISIONER_NAME
              value: k8s-sigs.io/nfs-subdir-external-provisioner
            - name: NFS_SERVER
              value: <replace with NFS IP>
            - name: NFS_PATH
              value: <replace with NFS Folder>
      volumes:
        - name: nfs-client-root
          nfs:
            server: <replace with NFS IP>
            path: <replace with NFS Folder>
```

## Create Role Based Access Control (RBAC)
This is needed to allow a pod to automatically create kubernetes objects that will provision Persistent Volumes

```bash
$ kubectl create -f deploy/rbac.yaml
```

## Update class storage file configuration
You may want to update the default configuration to not delete the entire folder when a persistent volume is destroyed.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: k8s-sigs.io/nfs-subdir-external-provisioner # or choose another name, must match deployment's env PROVISIONER_NAME'
parameters:
  pathPattern: "${.PVC.namespace}/${.PVC.annotations.nfs.io/storage-path}" # waits for nfs.io/storage-path annotation, if not specified will accept as empty string.
  onDelete: delete
```

Create the class storage object

```bash
$ kubectl create -f deploy/class.yaml
```

## Testing
This is optional but recommended specially if this is the first time that you are doing this. The repository has test deployments.

```bash
$ kubectl create -f deploy/test-claim.yaml -f deploy/test-pod.yaml
```

Check the NFS Server and see if there is a folder with a `SUCCESS` file name in it. If you see it, then all is set, you may delete the created test objects in your kubernetes installation.

```bash
$ kubectl delete -f deploy/test-pod.yaml -f deploy/test-claim.yaml
```