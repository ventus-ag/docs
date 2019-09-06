---
title: Install a LAMP web server on a Linux virtual machine
description: How to run a LAMP web server on a Linux virtual machine using Ventus cloud
tags: [ featured, tutorial ]
# permalink: /Install-LAMP/
---
# Install a LAMP web server on a Linux virtual machine
{: .no_toc }
---

The LAMP stack is a set of open source software used for web application development. For a web application to work, it has to include an operating system, a web server, a database and, a programming language. Each layer of software is vital for creating a database-driven and dynamic website.
The name LAMP is an acronym of the following programs:
- **L**inux Operating System
- **A**pache HTTP Server
- **M**ySQL database management system
- **P**HP programming language
It is critical to install each component of the stack separately and in a specific order.

This article walks you through how to deploy an Apache web server, MySQL, and PHP (the LAMP stack) on an Ubuntu VM in Ventus. To see the LAMP server in action, you can optionally install and configure a WordPress site. If you prefer the NGINX web server, see the LEMP stack tutorial. 

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create a virtual machine
---

For creating new virtual machine do the following:

1) On the main Navigation Panel go to `Cloud`, choose `Instances` and  click the plus `+` button from at the bottom-right of the screen.  

![](../../assets/img/new-linux-vm-V/instances-Ventus1.png)
![](../../assets/img/new-linux-vm-V/instances-Ventus0.png)

2) On the following page fill next information:
  - Instance Name (eg. "Test_LAMP")
  - Select a Boot source (eg. "ubuntu-1604-xenial")
  - Select a configuration (eg. "Small")
  - Select a SSH Key

![](../../assets/img/LAMP/LAMP1.png) 

{% include alert.html type="info" title="Keypair" content="You need to provide keypair which will be used for the instance. If you didn't have one, you can create new using this Core Tasks - SSH Keys" %} 

3) Hit `CREATE INSTANCE` button

4) Wait untill status of your Instance will be *RUNNING*
When the VM has been created, you can view instance details. Take note of the *publicIpAddress*. This address is used to access the VM in next steps.

![](../../assets/img/LAMP/LAMP2.png)  

## Install Apache, MySQL, and PHP
---

Run the following command to update Ubuntu package sources and install Apache, MySQL, and PHP. Note the caret (^) at the end of the command, which is part of the lamp-server^ package name.  

```
sudo apt update && sudo apt install lamp-server^  
```

You are prompted to install the packages and other dependencies. This process installs the minimum required PHP extensions needed to use PHP with MySQL.

## Verify installation and configuration
---
### Verify Apache

Check the version of Apache with the following command:  

```
apache2 -v
```

With Apache installed, and port 80 open to your VM, the web server can now be accessed from the internet. To view the Apache2 Ubuntu Default Page, open a web browser, and enter the public IP address of the VM. Use the public IP address you used to SSH to the VM:

![](../../assets/img/LAMP/LAMP3.png) 

### Verify and secure MySQL

Check the version of MySQL with the following command (note the capital V parameter):  

```
mysql -V
```    

To help secure the installation of MySQL, including setting a root password, run the `mysql_secure_installation` script.

```  
sudo mysql_secure_installation
``` 

You can optionally set up the Validate Password Plugin (recommended). Then, set a password for the MySQL root user, and configure the remaining security settings for your environment. We recommend that you answer "Y" (yes) to all questions.

If you want to try MySQL features (create a MySQL database, add users, or change configuration settings), login to MySQL. This step is not required to complete this tutorial.

```  
sudo mysql -u root -p
``` 

When done, exit the mysql prompt by typing \q.

### Verify PHP

Check the version of PHP with the following command:
  
`php -v`  

If you want to test further, create a quick PHP info page to view in a browser:

1) Create a basic PHP script and save it to the “web root” directory. This is necessary for Apache to find and serve the file correctly. This directory is located at `/var/www/html/`.

To create a file in that directory, type in the following command in the terminal:
  
```  
sudo nano /var/www/html/info.php 
```

That will open the bank file.

2) Inside the file, type in the valid PHP code:

```  
<?php  
phpinfo (); 
?>  
```

3) Press `CTRL + X` to save and close the file, and press y and `ENTER` to confirm.

4) Open a browser and type in **your IP address/info.php**
The output should display the details of the LAMP stack as seen in the image below:

![](../../assets/img/LAMP/LAMP4.png) 

## Install WordPress
---

If you want to try your stack, install a sample app. As an example, the following steps install the open source [WordPress](https://wordpress.org/) platform to create websites and blogs. Other workloads to try include [Drupal](https://www.drupal.org/) and [Moodle](https://moodle.org/).

This WordPress setup is only for proof of concept. To install the latest WordPress in production with recommended security settings, see the [WordPress documentation](https://codex.wordpress.org/Main_Page).

### Install the WordPress package

Run the following command:

```
sudo apt install wordpress    
```

### Configure WordPress

Configure WordPress to use MySQL and PHP.

1) In a working directory, create a text file `wordpress.sql` to configure the MySQL database for WordPress: 

```
sudo sensible-editor wordpress.sql
```  

2) Add the following commands, substituting a database password of your choice for *yourPassword* (leave other values unchanged). If you previously set up a MySQL security policy to validate password strength, make sure the password meets the strength requirements. Save the file.

```  
CREATE DATABASE wordpress;  
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER  
ON wordpress.*  
TO wordpress@localhost 
IDENTIFIED BY 'yourPassword';  
```

3) Run the following command to create the database:  

```
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf  
```

4) Because the file `wordpress.sql` contains database credentials, delete it after use:  

```
sudo rm wordpress.sql
```

5) To configure PHP, run the following command to open a text editor of your choice and create the file `/etc/wordpress/config-localhost.php`:  

```
sudo sensible-editor /etc/wordpress/config-localhost.php
```  

6) Copy the following lines to the file, substituting your WordPress database password for *yourPassword* (leave other values unchanged). Then save the file.

```  
<?php
define('DB_NAME', 'wordpress');  
define('DB_USER', 'wordpress');  
define('DB_PASSWORD', 'yourPassword');  
define('DB_HOST', 'localhost'); 
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');  
?>  
```

7) Move the WordPress installation to the web server document root: 

```
sudo ln -s /usr/share/wordpress /var/www/html/wordpress  
sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
``` 

8) Now you can complete the WordPress setup and publish on the platform. Open a browser and type in your **IP address/wordpress**. Substitute the public IP address of your VM. It should look similar to this image.

![](../../assets/img/LAMP/LAMP5.png) 

**Let's re-cap what we've done:**
- Create an Ubuntu VM
- Install Apache, MySQL, and PHP
- Verify installation and configuration
- Install WordPress on the LAMP server