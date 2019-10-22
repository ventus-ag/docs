---
title: Scaling
description: How to scale kubernetes cluster memory 
tags: [ featured, coretasks ]
# permalink: /Scaling/
---
# Scale-out and scale-in of Kubernetes cluster
---

In this task we will see how to scale-out and scale-in of Kubernetes cluster.

 

- go to Cloud page, choose Kubernetes and open your cluster:

![](../../assets/img/scaling/choose_cluster.png)   

- In opening cluster choose `Resize`  

![](../../assets/img/scaling/resize_node1.png) 

- Choose how many nodes you need and use action `resize cluster`  

![](../../assets/img/scaling/resize_node.png)  

- Wait until update progress will end:

![](../../assets/img/scaling/cluster_update.png)  

{% include alert.html type="info" title="Note" content="In the same way you can resize you cluster scling-in nodes." %} 

 **Let's see how it works on server side:** 

- Create Kubernetes cluster with existing parametrs (How to create Kubernetes cluster you can see in this core task <a href="http://docs.ventuscloud.eu/docs/coretasks/Kubernetes">Kubernetes cluster</a>):

```
Master count: 1
Node count: 1
Docker volume size (Gb): 30
Node flavor: Small
Master node flavor: Small
```

- Get access to this cluster (How to getting access to cluster you can see in this core task <a href="http://docs.ventuscloud.eu/docs/coretasks/access-by-cli">Access to Kubernetes Cluster using CLI</a>) 

- Create `deployment-test.yaml` file with existing lines: 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-test
  labels:
    app: nginx
spec:
  replicas: 5
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        resources:
          requests:
            memory: "400Mi"
        ports:
        - containerPort: 80
```
- Create deployment using `kubectl apply -f deployment-test.yaml` to create deployment.

- Using command `kubectl get pods` we will see that one of pods can't move in running state:

![](../../assets/img/scaling/get_pods.png)

- Using command `kubectl describe pod "pod name"` we will see that this pod have problem: `Insufficient memory` 

![](../../assets/img/scaling/Insufficient_memory.png)

- Now resize cluster and add one more node like we saw on steps earlier.

- Use the command `kubectl get nods` to make sure that one more node was added: 

![](../../assets/img/scaling/get_nodes.png)

- Use comand `kubectl get pods` and make sure that all pods in running state: 

![](../../assets/img/scaling/get_pods2.png)


#Tect github actins for usssing Spellcheker 