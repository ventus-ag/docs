---
title: ConfigMaps
description: How to create a ConfigMap 
tags: [ featured, coretasks ]
# permalink: /ConfigMaps/
---
# ConfigMaps
{: .no_toc }
---

In this page, you can find an explanation how to create ConfigMaps and configure Pods using data, stored in ConfigMaps. 
 


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## What is the ConfigMap 

**ConfigMap** - is configuration options for separate objects. ConfigMap contains key/value pairs with the values ranging from short literals to full config files.


## Create a ConfigMap

1) Use the `kubectl create configmap` command to create configmaps from directories, files, or literal values:

- `kubectl create configmap <map-name> <data-source>`

where `map-name` is the name you want to assign to the ConfigMap and `data-source` is the directory, file, or literal value to draw the data from.

For example: 
```
kubectl create configmap fortune-config --from-literal=sleep-interval=25
```
```console
configmap/fortune-config created
```

Now you can inspect your configmap with command:
```
kubectl get configmap fortune-config -o yaml
```
```console
apiVersion: v1
data:
  sleep-interval: "25"
kind: ConfigMap
metadata:
  creationTimestamp: "2020-02-24T16:10:50Z"
  name: fortune-config
  namespace: default
  resourceVersion: "2600128"
  selfLink: /api/v1/namespaces/default/configmaps/fortune-config
  uid: 04acfbfe-a208-43a1-a62e-7a9d8fb2f50e
```

2) Create ConfigMaps from directories

You can use `kubectl create configmap` to create a ConfigMap from multiple files in the same directory.

For exapmle:

Create the local directory:
```
mkdir -p configure-pod-container/configmap/
```

Download the sample files into `configure-pod-container/configmap/` directory:
```
wget https://kubernetes.io/examples/configmap/game.properties -O configure-pod-container/configmap/game.properties
```

Create the configmap:
```
kubectl create configmap game-config --from-file=configure-pod-container/configmap/
```
```console
configmap/game-config created
```

Now you can inspect your ConfigMap with command:
```
kubectl get configmap game-config -o yaml
```
```console
apiVersion: v1
data:
  game.properties: |-
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30
kind: ConfigMap
metadata:
  creationTimestamp: "2020-02-24T16:20:22Z"
  name: game-config
  namespace: default
  resourceVersion: "2601536"
  selfLink: /api/v1/namespaces/default/configmaps/game-config
  uid: a41a4fda-a464-4b5a-bbf3-1eb39af56775
```

3) Create ConfigMaps from files

You can use `kubectl create configmap` to create a ConfigMap from an individual file, or from multiple files.

For exapmle:
```
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties
```
```console
configmap/game-config-2 created
```

And use next command to inspect it: 
```
kubectl get configmap game-config-2 -o yaml
```
```console
apiVersion: v1
data:
  game.properties: |-
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30
kind: ConfigMap
metadata:
  creationTimestamp: "2020-02-24T16:22:44Z"
  name: game-config-2
  namespace: default
  resourceVersion: "2601885"
  selfLink: /api/v1/namespaces/default/configmaps/game-config-2
  uid: f7a453c9-5939-4b60-9e21-e1c4cdc831b3
```
 

## Using ConfigMap

1) Create `yaml` file and add the ConfigMap name under the `volumes` section of the Pod specification. 
This adds the ConfigMap data to the directory specified as `volumeMounts.mountPath` (in this case, `/etc/config`).
The `command` section references the `special.level` item stored in the ConfigMap:
```
vi pod-volume.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "ls /etc/config/" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: game-config
  restartPolicy: Never                          
```

2) Create a pod using `kubectl create` command:
```
kubectl create -f pod-volume.yaml
```
```console
pod/pod-volume created
```


After pod was created, you can use `kubectl logs` to see result: 
```
kubectl logs pod/pod-volume
```
```console
game.properties
```





