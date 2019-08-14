---
title: Creating a new Linux VM using Fleio
description: How to run Linux VM in Ventus Cloud by using intarface of Fleio
tags: [ featured, quickstart ]
# permalink: /new-Linux-VM-using-Fleio/
---
# Create a Linux virtual machine
---

{% include alert.html type="info" title="Ventus virtual machines (VMs) can be created through the Ventus portal" content="The Ventus portal is a browser-based user interface to create VMs and their associated resources. This quickstart shows you how to use the Ventus portal to deploy a Linux virtual machine (VM) running ubuntu-1604-xenial" %}
Instances are virtual machines that run inside the cloud.

## Create new instance

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
A SSH key which you previously added in SSH keys page. Also, you can add your own key or generate a new one to use - cklick the button -Add a new key-, assign a name and click the button **Generate key**. 
This field is optional,you can leave none selected. 
![](../../assets/img/new-linux-vm-fl/instances-fleio5.png)

- Root Password: You can view your typed password by clicking on the eye icon from right. The password should be at least 8 characters long and should contain at least one digit, letter or symbol.

3. Hit **Create Instance** and the new instance will be created.
![](../../assets/img/new-linux-vm-fl/instances-fleio6.png)



