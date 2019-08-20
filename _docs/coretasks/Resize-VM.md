---
title: Resize virtual machines
description: How to resize Linux VM in Ventus Cloud
tags: [ featured, coretasks ]
# permalink: /Resize-VM/
---
# Resize virtual machines
---

One of the great benefits of Ventus VMs is the ability to change the size of your VM based on the needs for CPU, Network, disk or memory requirements. 

**To resize the current instance:**  
- go to Instances page, choose instance, which size you want to change (eg. "Test" which flavor is 1x1)   
![](../../assets/img/resize/resize1.png)   

- click on bottom `more` and choose action `resize`  
![](../../assets/img/resize/resize2.png)  

- choose new flavor of instance you need and click `resize instance`  
![](../../assets/img/resize/resize3.png)  

- confirm the resizing - click `resize instance` again  
![](../../assets/img/resize/resize4.png)  

- wait until resize completed, login to your instance and confirm that the server is operational after resizing

- choose `Cofirm resize` from the instanse menu to confirm migration was successful, otherwise choose `Revert resize` to revert back to the initial state.  
![](../../assets/img/resize/resize5.png)    
 
Our instance was resized and its status becomes *running*.  
![](../../assets/img/resize/resize6.png) 

