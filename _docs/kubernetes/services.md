---
title: Services
description: How to create a new Service 
tags: [ featured, kubernetes ]
# permalink: /Services/
---
# Services
{: .no_toc }
---

In this page, you can find an explanation of how to create a new service in your Kubernetes cluster and understand the difference between types of services.
For service creation you will need Kubernetes cluster and access to cli. How to do that you can find in relevant core tasks:

 - [Kubernetes Cluster](https://ventuscloud.eu/docs/kubernetes/kubernetes-cluster)
 - [Access to Kubernetes Cluster using CLI](https://ventuscloud.eu/docs/kubernetes/access-by-cli)

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Introducing services

**Kubernetes Service** is a resource wich we create to make a single way of entry to a group of pods providing the same service. In Kubernetes, a Service is an abstraction which defines a logical set of Pods and a policy by which to access them. Each service has an IP address and port that never change while the service exists. We can connect to that IP and port, and those connections are then routed to one of the pods backing that service. 

Here we will consider three main types of services:
* **ClusterIP**  
* **NodePort**  
* **LoadBalancer**  

## ClusterIP Service creating

**ClusterIP** - Default Service type, wich exposes the Service on a cluster-internal IP and makes it only reachable from within the cluster.

**ClusterIP type** usually used for Services which no need to expose outside and should be used inside Kubernetes cluster. For example, internal data base, backend API, etc.

To create new Service which type is ClusterIP do next steps:

Create a `yaml` file called for example `kube-svc.yaml` with the following listing’s contents: 
```
vi kube-svc.yaml
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: kube
```   

You’re defining a service called *kube*, which will accept connections on port *80* and route each connection to port *8080* of one of the pods matching the app=kube; label - *selector*. 

Create the service by using next command:
```
kubectl create -f kube-svc.yaml
```
```console
service/kube created
```

Now you can list all Service resources in your namespace and see
that an internal cluster IP has been assigned to your service. To see it use the command:
```
kubectl get services
```
```console
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kube         ClusterIP   10.254.46.31   <none>        80/TCP    2m15s
kubernetes   ClusterIP   10.254.0.1     <none>        443/TCP   13d
```


See the type in our Service's details by using next command: 
```
kubectl describe svc kube
```
```console
Name:              kube
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=kube
Type:              ClusterIP
IP:                10.254.46.31
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

## NodePort Service creating

**NodePort** - Service type wich expose a static port on each Node’s IP. The Kubernetes provide port from a range 30000 - 32627 by default. Using a NodePort gives you the freedom to set up your own load balancing solution, to configure environments that are not fully supported by Kubernetes, or even to just expose one or more nodes’ IPs directly.

We can create NodePort Service with two ways:

1) Create a `yaml` file called for example `kube2-svc.yaml` with the following listing’s contents: 
```
vi kube2-svc.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube2-svc
spec:
  type: NodePort  
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: kube
```   

Create the NodePort service by using next command:
```
kubectl create -f kube2-svc.yaml
```
```console
service/kube2-svc created
```

2) If you already created Service you can edit it with command:
```
kubectl edit svc kube2-svc
```
Make nessesary changes and saved. It will be automatically applyed to your cluster.


Now you can list all Service resources in your namespace and see
that an internal cluster IP has been assigned to your service. To see it use the command:
```
kubectl get services
```
```console
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kube         ClusterIP   10.254.46.31     <none>        80/TCP         27m
kube2-svc    NodePort    10.254.191.189   <none>        80:32337/TCP   2m3s
kubernetes   ClusterIP   10.254.0.1       <none>        443/TCP        13d
```

To see the type in our Service's details we can use next command: 
```
kubectl describe svc kube2-svc
```
```console
Name:                     kube2-svc
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=kube
Type:                     NodePort
IP:                       10.254.191.189
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32337/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

## Loadbalancer Service creating

**LoadBalancer** - Service type wich have its own unique, publicly accessible IP address and will redirect all connections to your service. You can thus access your service through the load balancer’s IP address.

We can create LoadBalancer Service with two ways:

1) Create a `yaml` file called for example `kube3-svc.yaml` with the following listing’s contents: 
```
vi kube3-svc.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube3-svc
spec:
  type: LoadBalancer 
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: kube
```   

Create the NodePort service by using next command:
```
kubectl create -f kube3-svc.yaml
```
```console
service/kube3-svc created
```

2) If you already created Service you can edit it with command:
```
kubectl edit svc kube3-svc
```
Make nessesary changes and saved. It will be automatically applyed to your cluster.


Now you can list all Service resources in your namespace and see
that an internal cluster IP has been assigned to your service. To see it use the command:
```
kubectl get services
```
```console
NAME         TYPE           CLUSTER-IP        EXTERNAL-IP     PORT(S)        AGE
kube         ClusterIP      10.254.46.31      <none>          80/TCP         27m
kube2-svc    NodePort       10.254.191.189    <none>          80:32337/TCP   2m3s
kube3-svc    LoadBalancer   10.254.15.137     185.91.81.134   80:31270/TCP   51s
kubernetes   ClusterIP      10.254.0.1        <none>          443/TCP        13d
```

To see the type in our Service's details we can use next command: 
```
kubectl describe svc kube3-svc
```
```console
Name:                     kube3-svc
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=kube
Type:                     LoadBalancer
IP:                       10.254.15.137
LoadBalancer Ingress:     185.91.81.134
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31270/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age    From                Message
  ----    ------                ----   ----                -------
  Normal  EnsuringLoadBalancer  2m20s  service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   2m3s   service-controller  Ensured load balancer
```

