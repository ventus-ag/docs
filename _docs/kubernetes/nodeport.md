---
title: NodePort
description: How to create a NodePort Service 
tags: [ featured, kubernetes ]
# permalink: /NodePort/
---
# NodePort
{: .no_toc }
---

{% include alert.html type="info" title="Note" content="In this page, you can find an explanation of how to create a NodePort Service in your Kubernetes cluster. How to create Service with default type you can find in relevant core task:" %} 
 - <a href="https://ventuscloud.eu/docs/kubernetes/services">Services</a>


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## What is NodePort 

**NodePort** - Service type wich expose a static port on each Node’s IP. The Kubernetes provide port from a range 30000 - 32627 by default. Using a NodePort gives you the freedom to set up your own load balancing solution, to configure environments that are not fully supported by Kubernetes, or even to just expose one or more nodes’ IPs directly.

## NodePort Service creating

We can create NodePort Service with two ways:

1) Create a `yaml` file called for example `kube-svc.yaml` with the following listing’s contents: 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube-svc
spec:
  type: NodePort  
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: kube
```   
Use `kubectl create -f kube-svc.yaml` command to create NodePort Service.

2) If you already created Service you can edit it with command:
- `kubectl edit svc kube-svc`.

Make nessesary changes and saved. It will be automatically applyed to your cluster.

![](../../assets/img/services/new_service_created.png) 

Now you can list all Service resources in your namespace and see
that an internal cluster IP has been assigned to your service. To see it use the command:
- `kubectl get scv` 


Now we can use command: `kubectl describe svc kube-svc` to see NodePort type in our Service's details.

![](../../assets/img/services/describe_nodeport.png)  





