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

- `kubectl create configmap <map-name> <data-source>`

where <map-name> is the name you want to assign to the ConfigMap and <data-source> is the directory, file, or literal value to draw the data from.

For example: 

- `kubectl create configmap fortune-config --from-literal=sleep-interval=25`

![](../../assets/img/configmap/create_konfigmap.png)

 Now you can inspect your configmap with command:
- `kubectl get configmap fortune-config -o yaml`

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
- `kubectl get configmap game-config -o yaml`

![](../../assets/img/configmap/configmap_inpect2.png)

3) Create ConfigMaps from files

You can use `kubectl create configmap` to create a ConfigMap from an individual file, or from multiple files.

For exapmle:

- `kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties`

And use `kubectl get configmap game-config-2 -o yaml` to inspect it: 

![](../../assets/img/configmap/configmap_inpect3.png)








