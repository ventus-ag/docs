---
title: Creating a new Windows VM
description: How to create Windows VM in Ventus Cloud 
tags: [ featured, quickstart ]
# permalink: /new-Windows-Server-VM-using-Ventus Cloud/
---
# How to create Windows Server VM using Ventus Cloud 
{: .no_toc }
---

{% include alert.html type="info" title="Ventus virtual machines (VMs) can be created through the Ventus portal" content="The Ventus portal is a browser-based user interface to create VMs and their associated resources. This quickstart shows you how to use the Ventus portal to deploy a Windows Server virtual machine (VM) running Windows-server-2019" %}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new instance
---

{% include alert.html type="info" title="Instances are virtual machines that run inside the cloud."%}

For creating new instance do the following:

On the main Navigation Panel go to **Cloud**, choose **Instances** and  click the plus (+) button from at the bottom-right of the screen.
 
![](../../assets/img/win_server/WinVM.png)


On the following page fill in these fields:

- *Instance Name*: 
This will set the name in the Ventus database. The hostname inside the instance will not be set.(eg. "win-test")

![](../../assets/img/win_server/WinVM000.png)


- *Select a Boot source*: 
Select the image to boot from, from the list of available images. You can also boot from an instance snapshot if any is available. (eg. "Windows-server-2019")

- *Select storage type*: 
Select storage type "RBD" and provide preferred disc size

![](../../assets/img/win_server/WinVM001.png)


- *Select a configuration*: 
Configurations (flavors) hold information about the Instance memory, disk space and the number of virtual CPUs that will be available. (eg. "VC-2")

![](../../assets/img/win_server/WinVM002.png)


Hit **Create Instance** and the new instance will be created.

![](../../assets/img/win_server/WinVM002.png)


## Start / Shutdown / Reboot instance
---

There are two ways in interface for **Start / Shutdown / Restart** your instance:

First one is directly from instance page. In each instance box, there is one icon for **Start/Shutdown** and one icon for **Restart**. See next image for reference:

![](../../assets/img/win_server/WinVM005.png)

## Search for an instance
---

To search for an instance, type your instance name in the search box from the top of the instance page.

![](../../assets/img/win_server/WinVM004.png)


## Login into console
---

You can launch the web-console window directly from instance page or from instance details. Click on the icon *<>*  for launching the web-console.    
After the new window with the web-console will appear massage about first login, you should create password.

![](../../assets/img/win_server/WinVM003.png)


## How to open windows server using RDP
---

You should press **WIN + R** input **mstsc** and run

![](../../assets/img/win_server/mstsc.png)

You will see **RDP window**, than paste **your ip addres**

![](../../assets/img/win_server/ip.png)

**Example:**
![](../../assets/img/win_server/connection.png)

At last, input **Administrator** and your **password**

![](../../assets/img/win_server/login.png)

After **credentials** you will see result!

![](../../assets/img/win_server/WinVM008.png)


