---
title: Autoscaling
description: "How to use autoscaler: min and max count of nodes" 
tags: [ featured, coretasks ]
# permalink: /Autoscaling/
---
# Autoscaler: min and max count of nodes in Kubernetes cluster.
---

In this task we will see how use autoscaling in Kubernetes cluster.

 

- Go to Cloud page, choose Kubernetes and create your [Kubernetes cluster](https://ventuscloud.eu/docs/Kubernetes/Kubernetes%20Cluster) with existing parametrs and add tag `min_node_count=1`:

```
Master count: 1
Node count: 1
Docker volume size (Gb): 50
Node flavor: VC-2
Master node flavor: VC-2
```

![](../../assets/img/autoscaling/min_node_count.png)   

- Now connect to your cluster using [CLI](https://ventuscloud.eu/docs/Kubernetes/access-by-cli)  

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
- Create deployment using next command:   
```
kubectl apply -f deployment-test.yaml
```
```console
deployment.apps/deployment-test created
```
- Using command `kubectl get pods` we will see that one of pods can't move in running state:   
```
kubectl get pods
```
```console
NAME                               READY   STATUS    RESTARTS   AGE
deployment-test-55996c54bf-5dqwh   1/1     Running   0          4s
deployment-test-55996c54bf-gvcbn   1/1     Running   0          4s
deployment-test-55996c54bf-kvqf7   1/1     Running   0          4s
deployment-test-55996c54bf-qrzbv   0/1     Pending   0          4s
deployment-test-55996c54bf-xwngn   1/1     Running   0          4s
```

- Using command `kubectl describe pod "pod name"` we will see that this pod have problem: `Insufficient memory`    
```
kubectl describe pod deployment-test-55996c54bf-qrzbv
```
```console
Status:         Pending
IP:
Controlled By:  ReplicaSet/deployment-test-8676b58c7f
Containers:
  nginx:
    Image:      nginx:1.7.9
    Port:       80/TCP
    Host Port:  0/TCP
    Requests:
      memory:     400Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-vzppl (ro)
Conditions:
  Type           Status
  PodScheduled   False
Volumes:
  default-token-vzppl:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-vzppl
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason            Age                 From               Message
  ----     ------            ----                ----               -------
  Warning  FailedScheduling  74s (x4 over 4m5s)  default-scheduler  0/2 nodes are available: 1 Insufficient memory, 1 node(s) had taints that the pod didn't tolerate.
```

- Now create cluster and add tag `max_node_count=5` like we saw on steps earlier.

![](../../assets/img/autoscaling/max_node_count.png) 

- Connect to your cluster and create same deployment. 
- Wait 1 - 2 minutes while the cluster updated.
- Use comand `kubectl get pods` and make sure that all pods in running state: 
```
kubectl get pods
```
```console
NAME                               READY   STATUS    RESTARTS   AGE
deployment-test-55996c54bf-6lfpz   1/1     Running   0          19m
deployment-test-55996c54bf-gxcsr   1/1     Running   0          19m
deployment-test-55996c54bf-n87m7   1/1     Running   0          19m
deployment-test-55996c54bf-xkbqp   1/1     Running   0          19m
deployment-test-55996c54bf-zrd6f   1/1     Running   0          19m

```

- Use the command `kubectl get nods` to make sure that one more node was added: 
```
kubectl get nodes
```
```concole
NAME                         STATUS   ROLES    AGE   VERSION
test-qk7umk4e67yh-master-0   Ready    master   26m   v1.17.3
test-qk7umk4e67yh-node-0     Ready    <none>   24m   v1.17.3
test-qk7umk4e67yh-node-1     Ready    <none>   18m   v1.17.3
```

