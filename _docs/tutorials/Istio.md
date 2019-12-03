---
title: Install and and use Istio in Kubernetes
description: How to install and use istio in Kubernetes.
tags: [ featured, tutorial, kubernetes, istio ]
# permalink: /new-Linux-VM-using-Ventus/
---
# Install and use Istio in Kubernetes
{: .no_toc }
---

{% include alert.html type="info" title="What is Istio?" content="Istio - It is a service mesh that placed into existing distributed applications. Istio includes APIs that let it integrate into any logging platform, or telemetry or policy system and lets you successfully, and efficiently, run a distributed microservice architecture, and provides a uniform way to secure, connect, and monitor microservices." %}

{% include alert.html type="info" title="Goal of this tutorial" content="In this tutorial we will install Istio in Kubernetes, deploy the Bookinfo application used to demonstrate various Istio features and getting access to observabiliti console for Istio - Kiali." %}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Kubernetes cluster
---

1) Create new cluster using this tutorial: **Core Tasks/Clusters.** Use next parameters for your cluster:
  - `Master count`: 1
  - `Node count`: 3
  - `Docker volume size (GB)`: 30
  - `Node flavor`: VC-4
  - `Master node flavor`: VC-2

2) Wait until status of your cluster will be **Create completed**

![](../../assets/img/tutorials/tekton-pipelines/create_cluster_screenshot_4.png)

## Get access to your cluster using cli
---

To get access to your cluster you need **openstack** cli tool installed and connected to your project.

1) Install openstack cli tool by running two next commands one by one. First will install openstack and second one will install magnum client which is used to operate with kubenetes clusters:

`sudo pip install python-openstackclient`<br />
`sudo pip install python-magnumclient`

2) Get OpenRC file to connect to Ventus Cloud using cli:
  - On the main Navigation Panel go to **Cloud**, choose **API users** and click the plus (+) button at the bottom-right of the screen.
  - Fill out all fields and hit **ADD API USER**
  - For new user hit **Get OpenRC file** action

  <br />![](../../assets/img/tutorials/tekton-pipelines/get_openrc_file_1.png)

  - Select region and hit **GET FILE**

  <br />![](../../assets/img/tutorials/tekton-pipelines/get_openrc_file_2.png)
  

  - File named "openrc" will be downloaded to your machine

3) Execute "openrc" file starting with dot:
<br />`. openrc`

4) Provide your password and hit enter - this will authenticate you in the Ventus Cloud using created API user.

5) Run next command to get a list of all clusters:
<br />`openstack coe cluster list`

6) Run next command to get kubeconfig for your cluster:
<br />`openstack coe cluster config kubeflow`

7) Export path to created config for as KUBECONFIG env variable:
<br />`export KUBECONFIG=/home/ubuntu/config`

8) Run next command to test that you have access to the cluster and all pods are running:
<br />`kubectl get pods --all-namespaces`

![](../../assets/img/tutorials/tekton-pipelines/verify_1.png)

## Install Istio
---

Follow these steps to install Istio:

1) Download the release from the <a href ="https://github.com/istio/istio/releases/">Istio releases page</a>: 
- `wget https://github.com/istio/istio/releases/download/1.4.0/istio-1.4.0-linux.tar.gz`
- `tar -xvf istio-1.4.0-linux.tar.gz`

2) Move tto the Istio package direction:  
- `cd istio-1.4.0`

3) Add the istioctl client to your path:
- `export PATH=$PWD/bin:$PATH`

4) Install Istio:
- ` istioctl manifest apply --set profile=demo`

5) Verify that all Istio services are deployed and pods are in running state:
- `kubectl get svc -n istio-system`

![](../../assets/img/tutorials/Istio/verify_svc.png)

- `kubectl get pods -n istio-system` 

![](../../assets/img/tutorials/Istio/verify_pods.png)

## Deploying Bookinfo application 
---

{% include alert.html type="info" title="Bookinfo application" content="The application wich composed of four separate microservices used to demonstrate various Istio features. The application displays information about a book, similar to a single catalog entry of an online book store. Displayed on the page is a description of the book, book details (ISBN, number of pages, and so on), and a few book reviews." %}

1) Label the namespace that will host the application:
- `kubectl label namespace default istio-injection=enabled`

2) Deploy your application using the `kubectl` command:
- `kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml`

3) Verify that all services are deployed and pods are in running state:
- `kubectl get svc` 

![](../../assets/img/tutorials/Istio/verify_svc_2.png)

- `kubectl get pods` 

![](../../assets/img/tutorials/Istio/verify_pods_2.png)

4) Verify that the Bookinfo application is running:

```
kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
```
![](../../assets/img/tutorials/Istio/verify_bookinfo.png)

## Geting access to Bookinfo application 
---

1) Define the ingress gateway for the application:
- `kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml`

2) Confirm the gateway has been created:
- `kubectl get gateway` 

   ![](../../assets/img/tutorials/Istio/gateway.png)

3) Set the `INGRESS_HOST` and `INGRESS_PORT` variables for accessing the gateway:
```
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
```
4) Set `GATEWAY_URL`:
- `export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT`

5) Confirm that the Bookinfo application is accessible from outside the cluster, run the following `curl` command:
- `curl -s http://${GATEWAY_URL}/productpage | grep -o "<title>.*</title>"`

   ![](../../assets/img/tutorials/Istio/verify_bookinfo.png)

**Now we can view the Bookinfo web page following this staps:**
1) Get the LoadBalancer’s IP:
- `kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0]}'`

   ![](../../assets/img/tutorials/Istio/loadbalancer_ip.png)

2) Navigate to `https://<LoadBalancer's IP>/productpage`


Let's re-cap what we've done:
- Created new kubernetes cluster for Kubeflow.
- Deployed Kubeflow.
- Compiled mnist pipeline example.
- Uploaded pipeline to Kubeflow.
- Created and ran new experiment.
- As a result trained and served mnist model using Kubeflow in kubernetes.
