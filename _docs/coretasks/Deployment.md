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
Save this file with name like deployment.yaml and run command
```sh 
kubectl create -f deployment.yaml
```
2) Run **kubectl get deployments** to check if the Deployment was created. If the Deployment is still being created, the output is similar to the following:
```console
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3         3         3            3           9s
```

* **NAME** lists the names of the Deployments in the cluster.

* **DESIRED** displays the desired number of replicas of the application, which you define when you create the Deployment. This is the desired state.

* **CURRENT** displays how many replicas are currently running.

* **UP-TO-DATE** displays the number of replicas that have been updated to achieve the desired state.

* **AVALIBLE** displays how many replicas of the application are available to your users.

* **AGE** displays the amount of time that the application has been running.

---
# Updating a Deployment
{: .no_toc }

{% include alert.html type="info" title="Note" content="A Deployment’s rollout is triggered if and only if the Deployment’s Pod template (that is, .spec.template) is changed, for example if the labels or container images of the template are updated. Other updates, such as scaling the Deployment, do not trigger a rollout.." %}

1) Let’s update the nginx Pods to use the **nginx:1.9.1** image instead of the **nginx:1.7.9 image**.

```kubectl --record deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1```