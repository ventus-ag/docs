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
- the instance, from which we need to get access to the Kubernetes Cluster API, with the next parameters:
    * Name: Test_Inst
    * Image: ubuntu-1804-bionic
   
- the Kubernetes Cluster, with the next parameters:
    * Name: Test_Cluster

- API User, which has the next detailes and just loaded "openrc" file:
    * name: Test_User

    

## Get access to your cluster using CLI

1) Loggin to your Instance from which we need to get access to the Kubernetes Cluster API

2) Install openstack cli tool by running two next commands one by one: 

```
sudo pip install python-openstackclient
sudo pip install python-magnumclient
```

2) Place "openrc" file to your server Test_Inst

3) Execute "openrc" file starting with dot:

```
. openrc
```

4) Provide password of created API user and hit `enter` - this password will be used to authenticate you in the Ventus Cloud.

5) Run next command to get a list of all clusters:

```
openstack coe cluster list
```

6) Run next command to get kubeconfig file for your cluster:

```
mkdir ~/Test_Cluster
openstack coe cluster config --dir ~/Test_Cluster Test_Cluster
```

7) Export path to created config for as KUBECONFIG env variable:

```
export KUBECONFIG=~/Test_Cluster/config
```

8) Run next command to test that you have access to the cluster and all pods are running:

```
kubectl get nodes
kubectl get pods --all-namespaces
```











