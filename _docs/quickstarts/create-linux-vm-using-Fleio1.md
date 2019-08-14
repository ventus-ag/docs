---
title: Creating a new Linux VM by using Fleio
description: How to run Linux VM in Ventus Cloud by using intarface of Fleio
tags: [ featured, quickstart ]
# permalink: /new-Linux-VM-using-Fleio/
---
# Create a Linux virtual machine
---
---
---

{% include alert.html type="info" title="Ventus virtual machines (VMs) can be created through the Ventus portal" content="The Ventus portal is a browser-based user interface to create VMs and their associated resources. This quickstart shows you how to use the Ventus portal to deploy a Linux virtual machine (VM) running ubuntu-1604-xenial" %}

## Create new instance
---

{% include alert.html type="info" title="Instances are virtual machines that run inside the cloud."%}

For creating new instance do following:

1. On the main Navigation Panal go to **Cloud**, choose **Instances** and  click the plus (+) button from the bottom-right.  

![](../../assets/img/new-linux-vm-fl/instances-fleio1.png)

2. On the following page fill in these fields:

    - *Instance Name*: 
This will set the name in the Fleio and OpenStack database. The hostname inside the instance will not be set.(eg. "Test1")

![](../../assets/img/new-linux-vm-fl/instances-fleio2.png)

    - *Select a Region*: 
Select the region (location) of your instance.

    - *Select a Boot source*: 
Select the image to boot from, from the list of available images. You can also boot from an instance snapshot if any is available. (eg. "ubuntu-1604-xenial")

![](../../assets/img/new-linux-vm-fl/instances-fleio3.png)

    - *Select a configuration*: 
Configurations (flavors) hold information about the Instance memory, disk space and the number of virtual CPUs that will be available. (eg. "2x2")

![](../../assets/img/new-linux-vm-fl/instances-fleio4.png)

    - *Select a SSH Key*: 
A SSH key which you previously added in SSH keys page. Also, you can add your own key or generate a new one to use - cklick the button **Add a new key**, assign a name and click the button **Generate key**. 
This field is optional,you can leave none selected. 

![](../../assets/img/new-linux-vm-fl/instances-fleio5.png)

    - *Root Password*: 
    You can view your typed password by clicking on the eye icon from right. The password should be at least 8 characters long and should contain at least one digit, letter or symbol.


3. Hit **Create Instance** and the new instance will be created.

![](../../assets/img/new-linux-vm-fl/instances-fleio6.png)

## View instance details
---

{% include alert.html type="info" title="To find more details about your instance" content="just click on instance box from Instance page. A new page will open and here you’ll find all details about your instance" %}

Instance details area

    - *OS Icon*: Shows instance operating system icon (Centos, Ubuntu, etc.)
    - *Instance name*: The name in the Fleio and OpenStack database. Could be different from the hostname inside the instance.
    - *Instance status*: Depending on the instance state, it could be: Running, Stopped, Mounting, Rescued, etc.
    - *Quick actions*: Used for: Shutdown/Start, Restart, Launch Console, More (Rescue, Rebuild, Rename, Resize, Change password, Delete).
    - *Instance tab menu*: Access to following subpages: Info, System Log, Metrics, Snapshots, Volumes, History Log, Security Groups
    - *Specification*: Details about instance (installed image, flavor, RAM, CPUs, disk space)
    - *Network*: Details about network (private and public IPs)
    - *Other*: Other details like instance unique number and instance data creation

![](../../assets/img/new-linux-vm-fl/instances-fleio7.png)

##Search for an instance
---

To search for an instance, type your instance name in the search box from the top of the instance page.

![](../../assets/img/new-linux-vm-fl/instances-fleio8.png)

##Start / Shutdown / Reboot instance
---

There are two ways in interface for **Start / Shutdown / Restart** your instance:

- First one is directly from instance page. In each instance box, there is one icon for **Start/Shutdown** and one icon for **Restart**. See next image for reference:

![](../../assets/img/new-linux-vm-fl/instances-fleio9.png)

- Second way is from instance details (click on the instance you want)and there you will find, in the top-right of the instance window, one icon for Start/Shutdown and one icon for Restart, as you can see below:

![](../../assets/img/new-linux-vm-fl/instances-fleio10.png)

##Login into console
---

You can launch the console window directly from instance page or from instance details. Click on the icon *<>*  for launching the console.
After the new window with the console will appear, you can login into your VPS with your user details and start working.

![](../../assets/img/new-linux-vm-fl/instances-fleio11.png)

##Delete instance
___

If you want to delete an instance click the icon **More** and select **Delete**.
![](../../assets/img/new-linux-vm-fl/instances-fleio12.png)

Also whith this icon **More**, you can:
 - **Rescue instance** (recover your data)
 - **Rebuild instance** (reinstall instance)
 - **Rename instance**
 - **Resize instance** (upgrade CPU, RAM, HDD)
 - **Change root password**
