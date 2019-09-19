---
title: ConfigMaps
description: How to create a ConfigMap 
tags: [ featured, coretasks ]
# permalink: /ConfigMaps/
---
# ConfigMaps
{: .no_toc }
---

{% include alert.html type="info" title="Note" content="In this page, you can find an explanation how to create ConfigMaps and configure Pods using data stored in ConfigMaps." %} 
 


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## What is the ConfigMap 

**ConfigMap** - is configuration options for separate objects. ConfigMap contains key/value pairs with the values ranging from short literals to full config files.


## Create a ConfigMap

1) Use the `kubectl create configmap` command to create configmaps from directories, files, or literal values:

`kubectl create configmap <map-name> <data-source>`

where <map-name> is the name you want to assign to the ConfigMap and <data-source> is the directory, file, or literal value to draw the data from.

For example: 

`kubectl create configmap fortune-config --from-literal=sleep-interval=25`

![](../../assets/img/configmap/create_konfigmap.png)

 Now you can inspect your configmap with command:
`kubectl get configmap fortune-config -o yaml`

 ![](../../assets/img/configmap/configmap_inpect.png)

2) Create ConfigMaps from directories

You can use `kubectl create configmap` to create a ConfigMap from multiple files in the same directory.

For exapmle:

Create the local directory:
- `mkdir -p configure-pod-container/configmap/`

Download the sample files into `configure-pod-container/configmap/` directory:
- `wget https://kubernetes.io/examples/configmap/game.properties -O configure-pod-container/configmap/game.properties`

Create the configmap:
- `kubectl create configmap game-config --from-file=configure-pod-container/configmap/`


![](../../assets/img/configmap/create_konfigmap2.png)

Now you can inspect your configmap with command:
`kubectl get configmap game-config -o yaml`

![](../../assets/img/configmap/configmap_inpect2.png)

3) Create ConfigMaps from files

You can use `kubectl create configmap` to create a ConfigMap from an individual file, or from multiple files.

For exapmle:

`kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties`

And use `kubectl get configmap game-config-2 -o yaml` to inspect it: 

![](../../assets/img/configmap/configmap_inpect3.png)








![](../../assets/img/secrets/secret_creation.png)

Now you can check that the secret was created by command: `kubectl get secrets`

![](../../assets/img/secrets/check_secret.png)

And see details of it by command: `kubectl describe secrets/db-user-pass`

![](../../assets/img/secrets/describe_secret.png)

2) Creating a Secret from "yaml" file: 

For example, to store two strings in a Secret using the data field, convert them to base64 as follows:

- echo -n 'admin' | base64
- echo -n 'password' | base64

![](../../assets/img/secrets/base64.png)

Create a `yaml` file called for example `mysecret.yaml` with the following listing’s contents: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```   
Use `kubectl apply -f ./mysecret.yaml` command to create Secret.

![](../../assets/img/secrets/secret_creation_yaml.png)

Use command: `kubectl get secrets mysecret -o yaml` to see detailed information about Secret.

![](../../assets/img/secrets/secret_details.png)

## Using secrets

Secrets can be mounted as data volumes or be exposed as environment variables to be used by a container in a pod:

1) Mount secret as data volume: 

Create a `yaml` file called for example `nginx.yaml` with the following listing’s contents:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: newpod
spec:
  containers:
  - name: newpod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
```
Use `kubectl create -f nginx.yaml` command to create Pod.

`pod/newpod created`

Now we have pod `newpod` with secret `mysecret` whitch is located at /etc/foo.
We can see secret details using this commnads:

`kubectl exec newpod -it -- /bin/sh`
<br>`ls -l /etc/foo`
<br>`cat /etc/foo/username`
<br>`cat /etc/foo/password`

![](../../assets/img/secrets/secret_info.png)

We have been get values what we used and encoded earlier:
 <br> `username: YWRtaW4=`
 <br> `password: cGFzc3dvcmQ=`

 2) Using secret as Environment Variables:

 To use a secret in an environment variable in a pod: 
  - Create a secret or use an existing one. Multiple pods can reference the same secret.
  - Modify your Pod definition in each container that you wish to consume the value of a secret key to add an environment variable for each secret key you wish to consume. The environment variable that consumes the secret key should populate the secret’s name and key in: `env[].valueFrom.secretKeyRef`
  - Modify your image and/or command line so that the program looks for values in the specified environment variables

  This is an example of a pod that uses secrets from environment variables:

```yaml
  apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never
  ```
Now we have pod `secret-env-pod` with secret `mysecret` usiing as environment variables.
We can see secret details using this commnads:

`kubectl exec secret-env-pod -it -- /bin/sh`
<br>`echo $SECRET_USERNAME`
<br>`echo $PASSWORD`

![](../../assets/img/secrets/secret-env-pod.png)