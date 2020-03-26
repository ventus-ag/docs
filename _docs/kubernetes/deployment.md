---
title: Deployment
description: How to create a new Deployment
tags: [ featured, coretasks ]

---
# Deployment
{: .no_toc }

In this page, you can find an explanation of what is the Secret, how to create and use it.


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Deployment

**Deployment** controller provides declarative updates for Pods and ReplicaSets.

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
Save this file with name like deployment.yaml and run command:
```sh 
kubectl create -f deployment.yaml
```
2) Run ```kubectl get deployments```
to check if the Deployment was created. If the Deployment is still being created, the output is similar to the following:
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
## Updating a Deployment

{% include alert.html type="info" title="Note" content="A Deployment’s rollout is triggered if and only if the Deployment’s Pod template (that is, .spec.template) is changed, for example if the labels or container images of the template are updated. Other updates, such as scaling the Deployment, do not trigger a rollout.." %}


1) Let’s update the nginx Pods to use the ```nginx:1.9.1``` image instead of the ```nginx:1.7.9``` image.
```sh 
kubectl --record deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1
```
The output is similar to this:
```sh
deployment.apps/nginx-deployment image updated
```

2) To see the rollout status, run:
```sh
kubectl rollout status deployment.v1.apps/nginx-deployment
```

The output is similar to this:
  ```console
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
```
***or***
```console
deployment "nginx-deployment" successfully rolled out
```

Get more details on your updated Deployment:
```sh
kubectl get deployments
```

The output is similar to this:
```console
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3         3         3            3           50s
```

Run ```kubectl get rs```
to see that the Deployment updated the Pods by creating a new ReplicaSet and scaling it up to 3 replicas, as well as scaling down the old ReplicaSet to 0 replicas.


The output is similar to this:
```console
NAME                          DESIRED   CURRENT   READY     AGE
nginx-deployment-67594d6bf6   0         0         0         50s
nginx-deployment-6fdbb596db   3         3         3         9s
```

Running ```kubectl get pods```
should now show only the new Pods

The output is similar to this:
```console
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-6fdbb596db-5s62f   1/1       Running   0          50s
nginx-deployment-6fdbb596db-p568z   1/1       Running   0          50s
nginx-deployment-6fdbb596db-xt4dl   1/1       Running   0          50s
```

---
## Rolling Back a Deployment

{% include alert.html type="info" title="Note" content="For example, when the Deployment is not stable, such as crash looping. By default, all of the Deployment’s rollout history is kept in the system so that you can rollback anytime you want." %}

First, check the revisions of this Deployment:

```sh
kubectl rollout history deployment.v1.apps/nginx-deployment
```

The output is similar to this:
```console
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
3         kubectl deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.7.9 --record=true
4         kubectl deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1 --record=true
5         kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.91 --record=true

```


Rollback to a specific revision by specifying it with ```--to-revision```:
```sh
kubectl rollout undo deployment.v1.apps/nginx-deployment --to-revision=3
```

***Or*** rollback to the previous revision:

```sh
kubectl rollout undo deployment.v1.apps/nginx-deployment
```

The output is similar to this:
```console
deployment.apps/nginx-deployment
```
Check if the rollback was successful:

```sh
kubectl get deployment nginx-deployment
```
The output is similar to this:
```console
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3         3         3            3           1m
```

Get the description of the Deployment:

```sh
kubectl describe deployment nginx-deployment
```

The output is similar to this:
```console
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Wed, 18 Sep 2019 07:22:44 +0000
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision=6
                        kubernetes.io/change-cause=kubectl deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.7.9 --record=true
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-67594d6bf6 (3/3 replicas created)
Events:
  Type    Reason              Age   From                   Message
  ----    ------              ----  ----                   -------
  Normal  DeploymentRollback  5m    deployment-controller  Rolled back deployment "nginx-deployment" to revision 3
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled down replica set nginx-deployment-58c7645486 to 0
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled up replica set nginx-deployment-67594d6bf6 to 1
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled down replica set nginx-deployment-6fdbb596db to 2
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled up replica set nginx-deployment-67594d6bf6 to 2
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled down replica set nginx-deployment-6fdbb596db to 1
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled up replica set nginx-deployment-67594d6bf6 to 3
  Normal  ScalingReplicaSet   5m    deployment-controller  Scaled down replica set nginx-deployment-6fdbb596db to 0
```

## Scaling a Deployment

You can scale a Deployment:
```sh
kubectl scale deployment.v1.apps/nginx-deployment --replicas=10
```
The output is similar to this:
```sh
deployment.apps/nginx-deployment scaled
```
```sh
kubectl get deploy
```
The output is similar to this:
```console
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   10        10        10           10          1m
```


## Cleanup

```sh
kubectl delete -f deployment.yaml
```
The output is similar to this:
```console
deployment.apps "nginx-deployment" deleted
```