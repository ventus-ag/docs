---
title: Kubernetes Cluster
description: How to create a new Cluster 
tags: [ featured, kubernetes ]
# permalink: /Clusters/
---
# Kubernetes Cluster
{: .no_toc }

A **Kubernetes Cluster** is a group of logical objects, each of which is called a Node. A Kubernetes cluster can contain zero or more nodes.

In this page, you can find an explanation of how to create new Kubernetes Cluster and other Kubernetes Cluster management actions.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Kubernetes Cluster

For creating new Kubernetes Cluster do following:

1) On the main Navigation Panel go to  **Cloud**, choose **Kubernetes** and click the floating button **+** present in the lower right corner   

![](../../assets/img/coretasks/Kubernetes/cluster_create_1.png)
![](../../assets/img/new-linux-vm-V/instances-Ventus0.png)  

2) On the following page enter the `Cluster name`, and select `Cluster template` 

![](../../assets/img/coretasks/Kubernetes/cluster_create_2.png) 

3) Then fill the next fields: 
- `Master count`  
- `Node count`  
- `Node flavor`    
- `Docker volume size (GB)`
- `Master node flavor`  
- `Keypair`  

![](../../assets/img/coretasks/Kubernetes/cluster_create_3.png)

4) Hit `Create Cluster` and the new Kubernetes Cluster will be added. Estimate creation time about 5 minutes.

![](../../assets/img/clusters/clusters7.png)    

## View Kubernetes Cluster details
To find more details about your Kubernetes Cluster just click on cluster box from Kubernetes page. A new page will open and here you’ll find all details about your Kubernetes Cluster:

- **Cluster name**
- **Cluster status**
- **INFO**: 
    - ID
    - Created at
    - Updated at
    - Cluster template
    - Status reason  
- **NODES**:
    - Master count
    - Node count
    - API Address
    - Master Addresses
    - Node Addresses    
- **MISCELLANEOUS**:
    - Discovery URL
    - Cluster create timeout
    - Keypair
    - Master flavor
    - Node flavor
    - Health status  
- **LABELS**
And few icons of quick actions:
    - *Resize*
    - *Get certificate*
    - *Delete*  

![](../../assets/img/coretasks/Kubernetes/cluster_create_4.png) 

![](../../assets/img/coretasks/Kubernetes/cluster_create_5.png) 

## Delete a Kubernetes Cluster
To delete a Kubernetes Cluster, just click on the delete button in the card or list view and on the following page confirm your action as shown below:  

![](../../assets/img/coretasks/Kubernetes/cluster_delete_3.png) 
![](../../assets/img/coretasks/Kubernetes/cluster_delete_2.png) 



