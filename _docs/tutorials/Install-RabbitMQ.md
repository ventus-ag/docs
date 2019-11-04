---
title: Install RabbitMQ on CentOS / RHEL & Ubuntu / Debian based systems using Ventus Cloud
description: How install RabbitMQ on CentOS / RHEL & Ubuntu / Debian based systems using Ventus Cloud
tags: [ featured, tutorial RabbitMQ]
# permalink: /Install-/
---
# Install RabbitMQ on CentOS / RHEL & Ubuntu / Debian based systems 
{: .no_toc }
---
## Table of contents
{: .no_toc .text-delta }

* TOC
{:toc}

## Intoduction

**Message Brokers** are usually application stacks with dedicated pieces covering the each stage of the exchange setup. From accepting a message to queuing it and delivering it to the requesting party, brokers handle the duty which would normally be much more cumbersome with non-dedicated solutions or simple hacks such as using a database, cron jobs, etc. They simply work by dealing with queues which technically constitute infinite buffers, to put messages and pop-and-deliver them later on to be processed either automatically or by polling.

Using this technology allows you to cover many areas, including, but not limited to:
* Allowing web servers to respond to requests quickly instead of being forced to perform resource-heavy procedures on the spot
* Distributing a message to multiple recipients for consumption (e.g. processing)
* Letting offline parties (i.e. a disconnected user) fetch data at a later time instead of having it lost permanently
* Introducing fully asynchronous functionality to the backend systems
* Ordering and prioritising tasks
* Balancing loads between workers
* Greatly increase reliability and uptime of your application
* and much more

**Advanced Message Queuing Protocol (AMQP)** is a widely accepted open-source standard for distributing and transferring messages from a source to a destination. As a protocol and standard, it sets a common ground for various applications and message broker middlewares to interoperate without encountering issues caused by individually set design decisions.

**RabbitMQ** is an open-source message-broker application stack which implements the Advanced Message Queuing Protocol (AMQP). It is a lightweight application available for most of the popular operating systems. RabbitMQ supports multiple messaging protocols. It can be easily deployed in a distributed and federated configurations to meet high-scale, high-availability requirements.

RabbitMQ works by offering an interface, connecting message senders (Publishers) with receivers (Consumers) through an exchange (Broker)which distributes the data to relevant lists (Message Queues).

This tutorial will help you to install RabbitMQ on Ubuntu / Debian Based Systems

## Prerequisites

Before you can start this guide you will need to create a new Ubuntu or Centos server. To do this follow the instructions from the next quickstarts **Creating a new Linux VM using Ventus** (<https://docs.ventuscloud.eu/docs/quickstarts/create-linux-vm-using-Ventus>).
After creating you need to connect to your remote Ubuntu or Centos server using the ssh protocol and IP of this server.

When you are finished the setup steps, log into your server as your sudo user and continue below.

## Installing RabbitMQ

RabbitMQ packages are distributed both with CentOS / RHEL & Ubuntu / Debian based systems. However, they are - like with most applications - outdated. The recommended way to get RabbitMQ on your system is therefore to download the package online and install manually.

### Installing on CentOS / RHEL Based Systems

Before installing RabbitMQ, we need to get its main dependencies such as Erlang. However, first and foremost we should update our system and its default applications.

Run the following to update our droplet:
```
yum -y update
```

And let’s use the below commands to get Erlang on our system. Add and enable relevant application repositories (we are also enabling third party remi package repositories):
```
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
sudo rpm -Uvh remi-release-6*.rpm epel-release-6*.rpm
```

Finally, download and install Erlang:
```
yum install -y erlang
```

Once we have Erlang, we can continue with installing RabbitMQ:
* Download the latest RabbitMQ package using wget:
```
wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.2.2/rabbitmq-server-3.2.2-1.noarch.rpm
```
* Add the necessary keys for verification:
```
rpm --import http://www.rabbitmq.com/rabbitmq-signing-key-public.asc
```
* Install the .RPM package using YUM:
```
yum install rabbitmq-server-3.2.2-1.noarch.rpm
```
* Restart the service:
```
/sbin/service rabbitmq-server restart
```

### Installing on Ubuntu / Debian Based Systems

The process for downloading and installing RabbitMQ on Ubuntu and Debian will be similar to CentOS due to our desire of having a more recent version. 

Let’s begin with updating our system’s default application toolset:
```
apt-get    update 
apt-get -y upgrade
```

Enable RabbitMQ application repository:
```
echo "deb http://www.rabbitmq.com/debian/ testing main" >> /etc/apt/sources.list
```

Add the verification key for the package:
```
curl http://www.rabbitmq.com/rabbitmq-signing-key-public.asc | sudo apt-key add -
```

Update the sources with our new addition from above:
```
apt-get 
```

And finally, download and install RabbitMQ:
```
sudo apt-get install rabbitmq-server
```

In order to manage the maximum amount of connections upon launch, open up and edit the following configuration file using nano:
```
sudo nano /etc/default/rabbitmq-server
```
Uncomment the `limit` line (i.e. remove `#`) before saving and exit by pressing `CTRL+X` followed with `Y`.

## Managing RabbitMQ

As we have mentioned before, RabbitMQ is very simple to get started with. Using the instructions below for your system, you can quickly manage its process and have it running at the system start-up (i.e. boot).

RabbitMQ Management Console is one of the available plugins that lets you monitor the [RabbitMQ] server process through a web-based graphical user interface (GUI).

Using this console you can:

* Manage exchanges, queues, bindings, users
* Monitor queues, message rates, connections
* Send and receive messages
* Monitor Erlang processes, memory usage
* And much more

To enable RabbitMQ Management Console, run the following:
```
sudo rabbitmq-plugins enable rabbitmq_management
```
Once you’ve enabled the console, it can be accessed using your favourite web browser by visiting:
```
http://[your server's IP]:15672/
```
![](../../assets/img/rabbit/1.png)

By default, the guest user exists and can connect only from localhost. You can login with this user locally with the password “guest”

To be able to login on the network, create an admin user like below:
```
sudo rabbitmqctl add_user admin StrongPassword
sudo rabbitmqctl set_user_tags admin administrator
```

Use the created user to login to the RabbitMQ management interface. 

![](../../assets/img/rabbit/2.png)

### Managing on CentOS / RHEL Based Systems
Upon installing the application, RabbitMQ is not set to start at system boot by default.

To have RabbitMQ start as a daemon by default, run the following:
```
chkconfig rabbitmq-server on
```

To start, stop, restart and check the application status, use the following:
* To start the service:
```
/sbin/service rabbitmq-server start
```
* To stop the service:
```
/sbin/service rabbitmq-server stop
```
* To restart the service:
```
/sbin/service rabbitmq-server restart
```
* To check the status:
```
/sbin/service rabbitmq-server status
```

### Managing on Ubuntu / Debian Based Systems

For managing RabbitMQ on Ubuntu / Debian based systems use the following commands:
* To start the service:
```
service rabbitmq-server start
```
* To stop the service:
```
service rabbitmq-server stop
```
* To restart the service:
```
service rabbitmq-server restart
```
* To check the status:
```
service rabbitmq-server status
```

And that’s it! You now have your own message queue working on your virtual server.

## Configuring RabbitMQ

RabbitMQ by default runs with its standard configuration. In general, it does not require much tempering with for most needs as long as everything runs smoothly.

To learn about configuring it for custom needs, check out its documentation for <https://www.rabbitmq.com/configure.html>







