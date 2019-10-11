---
title: Scaling
description: How to scale kubernetes cluster memory 
tags: [ featured, coretasks ]
# permalink: /Scaling/
---
# Scaling cluster memory
---

In this task we will see how to scale cluster memory.



**To scale cluster memory:**  
- go to Cloud page, choose Kubernetes and open your cluster:
![](../../assets/img/scaling/choose_cluster.png)   

- In opening cluster choose `resize`  
![](../../assets/img/scaling/resize_node1.png) 

- Choose how much nodes you need and use action `resize cluster`  
![](../../assets/img/scaling/resize_node.png)  

- Wait untill update progress will end:  
![](../../assets/img/scaling/cluster_update.png)  

 **Let's see how it works on server side:** 

- Create Kubernetes cluster with existing parametrs (How to create Kubrnetes cluster you can see in this core task: <a href="http://docs.ventuscloud.eu/docs/coretasks/Kubernetes">Kubernetes cluster</a>):

```
Master count: 1
Node count: 1
Docker volume size (Gb): 30
Node flavor: Small
Master node flavor: Small
```

- choose `Cofirm resize` from the instanse menu to confirm migration was successful, otherwise choose `Revert resize` to revert back to the initial state.  
![](../../assets/img/resize/resize5.png)    
 
Our instance was resized and its status becomes *running*.  
![](../../assets/img/resize/resize6.png) 

**For example, see a quick recap**
![](../../assets/img/resize/resize.gif)
