---
title: Clusters
description: How to create a new Cluster 
tags: [ featured, coretasks ]
# permalink: /Clusters/
---
# Clusters
{: .no_toc }

{% include alert.html type="info" title="Note" content="A Cluster is a group of logical objects, each of which is called a Node. A cluster can contain zero or more nodes." %}  

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Cluster

For creating new Cluster do following:

1) On the main Navigation Panel go to  `Cloud`, choose `Clusters` and click the floating button `+` present in the lower right corner   

![](../../assets/img/clusters/clusters1.png)  

2) On the following page enter the `Cluster name` (Eg. "demo1"), and select `Cluster template`

![](../../assets/img/clusters/clusters6.png) 

3) Then fill the next fields: 
    `Master count`  
    `Node count`  
    `Node flavor`    
    `Master node flavor`  
    `Keypair`  

![](../../assets/img/clusters/clusters2.png)

4) Hit `Create Cluster` and the new Cluster will be added. Estimate creation time about 5 minutes.

![](../../assets/img/clusters/clusters7.png)    

## View Cluster details
To find more details about your Cluster just click on Cluster box from Clusters page. A new page will open and here you’ll find all details about your Cluster:

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

![](../../assets/img/clusters/clusters3.png) 

## Delete a Cluster
To delete a Cluster, just click on the delete button in the card or list view and on the following page confirm your action as shown below:  

![](../../assets/img/clusters/clusters4.png) 
![](../../assets/img/clusters/clusters5.png) 

**For example, see a quick recap**
![](../../assets/img/clusters/clusters.gif)

