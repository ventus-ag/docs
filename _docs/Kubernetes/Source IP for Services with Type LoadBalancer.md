---
title: Source IP for Services with Type LoadBalancer
description: How to get Source IP  with Type LoadBalancer
tags: [ featured, Kubernetes ]
# permalink: /Source IP for Services with Type LoadBalancer/
---
# Source IP 
{: .no_toc }
In this page, you can find an explanation of how to get the source IP of your app in your Kubernetes cluster. 

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Deploy web application

To show you how we could get the source IP we took small app that echoes back the source IP of requests. To deploy it use command:
```
kubectl run source-ip-app --image=k8s.gcr.io/echoserver:1.4
```
```console
deployment.apps/source-ip-app created
```

## Creation service with Type LoadBalancer

1) Create Service with Type `LoadBalancer` and annotation `loadbalancer.openstack.org/x-forwarded-for: "true"` using this `app-svc.yaml` file: 

```
vi app-svc.yaml
```
```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    loadbalancer.openstack.org/x-forwarded-for: "true"
  labels:
    run: source-ip-app
  name: source-ip-app-svc
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    run: source-ip-app
  type: LoadBalancer
```

Use next command to create it:
```
kubectl apply -f app-svc.yaml
```
```console
service/source-ip-app-svc created
```

Now get external ip of your service using command:
```
kubectl get svc
```


![](../../assets/img/Source IP with Type LoadBalancer/external_ip.png) 

## Getting Source IP

To get Source IP you need use command `curl` with external IP that you has been received in previous step.

In our case it's: 
```
curl 46.4.240.46
```

The output is:
```console
CLIENT VALUES:
client_address=10.100.1.1
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://46.4.240.46:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=46.4.240.46
user-agent=curl/7.58.0
x-forwarded-for=188.40.161.103
```
{% include alert.html type="info" title="Note" content="With a proxy that terminates the client connection and opens a new connection to your nodes/endpoints. In such cases the source IP will always be that of the cloud LB, not that of the client." %}

In our case it's an IP from line: `x-forwarded-for=188.40.161.103`


















