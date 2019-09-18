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
```sh
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
{: .no_toc }

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

Suppose that you made a typo while updating the Deployment, by putting the image name as ```nginx:1.91```instead of```nginx:1.9.1```:

```sh
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.91 --record=true
```
The output is similar to this:

```console
deployment.apps/nginx-deployment image updated
```
The rollout gets stuck. You can verify it by checking the rollout status:

```sh
kubectl rollout status deployment.v1.apps/nginx-deployment
```
The output is similar to this:

```console
Waiting for rollout to finish: 1 out of 3 new replicas have been updated...
```

Press ```Ctrl-C``` to stop the above rollout status watch.


Type ```kubectl get rs``` and you see that the number of old replicas


The output is similar to this:

```console
NAME                          DESIRED   CURRENT   READY     AGE 
nginx-deployment-58c7645486   1         1         0         5s  
nginx-deployment-67594d6bf6   0         0         0         50s 
nginx-deployment-6fdbb596db   3         3         3         50s 
```

Pod created by new ReplicaSet is stuck in an image pull loop.
To check that```kubectl get pods```

The output is similar to this:

```console
NAME                                READY     STATUS             RESTARTS   AGE
nginx-deployment-58c7645486-j8dx4   0/1       ImagePullBackOff   0          22s
nginx-deployment-6fdbb596db-5s62f   1/1       Running            0          2m
nginx-deployment-6fdbb596db-p568z   1/1       Running            0          2m
nginx-deployment-6fdbb596db-xt4dl   1/1       Running            0          2m
```
