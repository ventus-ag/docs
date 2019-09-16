---
title: Access to the Kubernetes Cluster using CLI
description: How to get access to the Kubernetes Cluster using CLI 
tags: [ featured, coretasks ]
# permalink: /access-by-cli/
---
# Access to your cluster using CLI
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Prerequisites

For this coretask we suppose that in Ventus cloud we've already created:
- the instance, with the next parameters:
    * Name: Test_Inst
    * Image: ubuntu-1804-bionic
    * Flavor: Small
    * Public IP: 

- the kubernetes cluster, with the next parameters:
    * Name: Test_Cluster
    * Master count: 1
    * Node count: 1
    * Docker volume size (GB): 30
    * Node flavor: Medium 
    * Master node flavor: Small

- API User, which has the next detailes and just loaded "openrc" file:
    * name: Test_User
    * password: 12344321
    

## Get access to your cluster using CLI

To get access to your cluster you need **openstack** cli tool installed and connected to your project.

1) Install openstack cli tool by running two next commands one by one. First will install openstack and second one will install magnum client which is used to operate with kubenetes clusters: 

```
sudo pip install python-openstackclient
sudo pip install python-magnumclient
```

2) Execute "openrc" file starting with dot:

```
. openrc
```

3) Provide your password and hit `enter` - this will authenticate you in the Ventus Cloud using created API user.

4) Run next command to get a list of all clusters:
```
openstack coe cluster list
```

5) Run next command to get kubeconfig file for your cluster:

```
openstack coe cluster config kubeflow
```

6) Export path to created config for as KUBECONFIG env variable:

```
export KUBECONFIG=/root/config
```

8) Run next command to test that you have access to the cluster and all pods are running:
```
kubectl get pods --all-namespaces
```











