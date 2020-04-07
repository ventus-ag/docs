---
title: Creating a new Linux VM using Ventus Cloud
description: How to create Linux VM in Ventus Cloud 
tags: [ featured, quickstart ]
# permalink: /new-Linux-VM-using-Ventus-Cloud/
---
# How to create Linux VM in Ventus Cloud 
{: .no_toc }
---

{% include alert.html type="info" title="Ventus virtual machines (VMs) can be created through the Ventus portal" content="The Ventus portal is a browser-based user interface to create VMs and their associated resources. This quickstart shows you how to use the Ventus portal to deploy a Linux virtual machine (VM) running ubuntu-server-18.04." %}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new instance
---

{% include alert.html type="info" title="Instances are virtual machines that run inside the cloud."%}

For creating new instance do the following:

1) On the main Navigation Panel go to **Cloud**, choose **Instances** and  click the plus (+) button from at the bottom-right of the screen.
 
![](../../assets/img/new-linux-vm-V/instances-Ventus1.png)
![](../../assets/img/new-linux-vm-V/instances-Ventus0.png)


2) On the following page fill in these fields:

- *Instance Name*: 
This will set the name in the Ventus database. The hostname inside the instance will not be set.(eg. "Test1")

![](../../assets/img/new-linux-vm-V/instances-Ventus2.png)


- *Select a Boot source*: 
Select the image to boot from, from the list of available images. You can also boot from an instance snapshot if any is available. (eg. "ubuntu-server-18.04-LTS-20200131.1")

- *Select storage type*: 
Select storage type "RBD" and provide preferred disc size

![](../../assets/img/new-linux-vm-V/instances-Ventus3.png)



- *Select a configuration*: 
Configurations (flavors) hold information about the Instance memory, disk space and the number of virtual CPUs that will be available. (eg. "VC-2")

![](../../assets/img/new-linux-vm-V/instances-Ventus4.png)

- *Network selection*: 
Select network (public or private)

- *Select a SSH Key*: 
A SSH key which you previously added in SSH keys page. Also, you can add your own key or generate a new one to use - cklick the button **Add a new key**, assign a name and click the button **Generate key**. 
This field is optional,you can leave none selected. 

![](../../assets/img/new-linux-vm-V/instances-Ventus5.png)



- *Root Password*: 
You can view your typed password by clicking on the eye icon from right. The password should be at least 8 characters long and should contain at least one digit, letter or symbol.

![](../../assets/img/new-linux-vm-V/instances-Ventus55.png)



3) Hit **Create Instance** and the new instance will be created.


![](../../assets/img/new-linux-vm-V/instances-Ventus6.png)

**For example, see a quick recap**  
![](../../assets/img/new-linux-vm-V/001.gif)


## View instance details
---

{% include alert.html type="info" title="To find more details about your instance" content="just click on instance box from Instance page. A new page will open and here you’ll find all details about your instance" %}

Instance details area:

- *OS Icon*: Shows instance operating system icon (Centos, Ubuntu, etc.)
- *Instance name*: Could be different from the hostname inside the instance.
- *Instance status*: Depending on the instance state, it could be: Running, Stopped, Mounting, Rescued, etc.
- *Quick actions*: Used for: Shutdown/Start, Restart, Launch Console, More (Rescue, Rebuild, Rename, Resize, Change password, Delete).
- *Instance tab menu*: Access to following subpages: Info, System Log, Metrics, Snapshots, Volumes, History Log, Security Groups
- *Specification*: Details about instance (installed image, flavor, RAM, CPUs, disk space)
- *Network*: Details about network (private and public IPs)
- *Other*: Other details like instance unique number and instance data creation.

![](../../assets/img/new-linux-vm-V/instances-Ventus7.png)



## Search for an instance
---

To search for an instance, type your instance name in the search box from the top of the instance page.

![](../../assets/img/new-linux-vm-V/instances-Ventus8.png)



## Start / Shutdown / Reboot instance
---

There are two ways in interface for **Start / Shutdown / Restart** your instance:

First one is directly from instance page. In each instance box, there is one icon for **Start/Shutdown** and one icon for **Restart**. See next image for reference:

![](../../assets/img/new-linux-vm-V/instances-Ventus9.png)


Second way is from instance details (click on the instance you want)and there you will find, in the top-right of the instance window, one icon for Start/Shutdown and one icon for Restart, as you can see below:

![](../../assets/img/new-linux-vm-V/instances-Ventus10.png)


## Login into console
---

You can launch the web-console window directly from instance page or from instance details. Click on the icon *<>*  for launching the web-console.    
After the new window with the web-console will appear, you can login into your VPS with your user details and start working.    
You can also connect to your instance through another Terminal using the SSH protocol and instance-IP:  
```
ssh <IP of instance>
```

## Manage an instance
---

The icon **More** is responsible for managing an instance
So whith this icon **More**, you can:
 - **Rescue instance** (recover your data)
 - **Rebuild instance** (reinstall instance)
 - **Rename instance**
 - **Resize instance** (upgrade CPU, RAM, HDD)
 - **Change root password**

![](../../assets/img/new-linux-vm-V/instances-Ventus12.png)


## Delete instance  
---

If you want to delete an instance click the icon **More** and select **Delete**

![](../../assets/img/new-linux-vm-V/instances-Ventus13.png)  
![](../../assets/img/new-linux-vm-V/View instance details.gif)


