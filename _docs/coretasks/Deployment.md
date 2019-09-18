---
title: Deployment
description: How to create a new Deployment
tags: [ featured, coretasks ]

---
# Deployment
{: .no_toc }

{% include alert.html type="info" title="Note" content="A Deployment controller provides declarative updates for Pods and ReplicaSets." %}

## Table of contents
{: .no_toc .text-delta }

## Create new Deployment

1) The following is an example of a Deployment. It creates a ReplicaSet to bring up three nginx Pods

```yaml
controllers/nginx-deployment.yaml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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
        ports:
        - containerPort: 80
```