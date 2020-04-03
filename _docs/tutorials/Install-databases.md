---
title: Install and set up most popular databases in Ventus Cloud
description: How install and set up databases such as MySQL, PostgreSQL and Apache Cassandra NoSQL DB on Ubuntu system using Ventus Cloud
tags: [ featured, tutorial ]
# permalink: /Install-databases/
---
# How install and set up such databases as the MySQL, PostgreSQL and Apache Cassandra NoSQL DB on Ubuntu system using Ventus Cloud
{: .no_toc }
---

Databases store information and its contents can be everything from product catalogs to repositories of customer information.

In this tutorial, we’ll go over some of the most popular databases currently used such as MySQL, PostgreSQL and Apache Cassandra NoSQL DB on Linux VM in Ventus Cloud.


## Table of contents
{: .no_toc .text-delta }

* TOC
{:toc}

## Prerequisites

Before you can start this guide you will need to create a new Ubuntu server. To do this follow the instructions from the next quickstarts [Create a Linux virtual machine](https://ventuscloud.eu/docs/quickstarts/create-linux-vm-using-ventus).
After creating you need to connect to your remote Ubuntu server using the ssh protocol and IP of this server and continue below.

## Install MySQL Server on Ubuntu
-----

MySQL is the world's most popular open-source database. Despite its powerful features, MySQL is simple to set up and easy to use. 

Few features of this tool are: 
* high-speed data processing, 
* use of triggers increases productivity, 
* with rollback and commit helps in data recovery if required.

This tutorial describes a basic installation of a MySQL database server on Ubuntu Linux. You might need to install other packages to let applications use MySQL, like extensions for PHP. We also explain how to perform some basic operations with MySQL using the mysql client. It is a good choice if you know that you need a database but don’t know much about all the available options.

**Step 1 - Installing MySQL**  

Install the MySQL server by using the Ubuntu package manager:
```
sudo apt-get update
sudo apt-get install mysql-server
```
The installer installs MySQL and all dependencies.

Check the version of MySQL with the following command (note the capital V parameter):  

```
mysql -V
```    

If the secure installation utility does not launch automatically after the installation completes, enter the following command:
```
sudo mysql_secure_installation utility
```
This utility prompts you to define the mysql root password and other security-related options, including removing remote access to the root user and setting the root password. You will be asked a series of questions. We have provided the answers below for simplicity:

```
Set root password? [Y/n] Y
New password: Enter your password here
Re-enter new password: repeat your password
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
Your MySQL database is now secure. Type the command below to enable it during boot:

```
sudo systemctl enable mariadb.service
```

At this point, your database system is now set up and we can move on.

**Step 2 - Allowing remote access**  

If you have iptables enabled and want to connect to the MySQL database from another machine, you must open a port in your server’s firewall (the default port is 3306). You don’t need to do this if the application that uses MySQL is running on the same server.

Run the following command to allow remote access to the mysql server:
```
sudo ufw enable
sudo ufw allow mysql
```

After the installation is complete, you can start the database service by running the following command. If the service is already started, a message informs you that the service is already running:
```
sudo systemctl start mysql
```

To ensure that the database server launches after a reboot, run the following command:
```
sudo systemctl enable mysql
```

**Step 3 - Configuring interfaces**  

MySQL, by default is no longer bound to ( listening on ) any remotely accessible interfaces. Edit the “bind-address” directive in /etc/mysql/mysql.conf.d/mysqld.cnf:  
```
bind-address		= 127.0.0.1 ( The default. )
bind-address		= XXX.XXX.XXX.XXX ( The ip address of your Public Net interface. )
bind-address		= ZZZ.ZZZ.ZZZ.ZZZ ( The ip address of your Service Net interface. )
bind-address		= 0.0.0.0 ( All ip addresses. )
```

Restart the mysql service.
```
sudo systemctl restart mysql
```

**Step 4 - Start the mysql shell**  

There is more than one way to work with a MySQL server, but this article focuses on the most basic and compatible approach, the `mysql shell`.

1) At the command prompt, run the following command to launch the mysql shell and enter it as the root user:
```
sudo mysql -u root -p
```

2) When you’re prompted for a password, enter the one that you set at installation time, or if you haven’t set one, press `Enter` to submit no password.

The following `mysql shell` prompt should appear:
```
mysql>
```

**Step 5 - Setting the root password**  
If you logged in by entering a blank password, or if you want to change the root password that you set, you can create or change the password.

1) For versions earlier than MySQL 5.7, enter the following command in the `mysql shell`, replace `password` with your new password:
```
UPDATE mysql.user SET Password = PASSWORD('password') WHERE User = 'root';
```

For version MySQL 5.7 and later, enter the following command in the `mysql shell`, replacing `password` with your new password:
```
UPDATE mysql.user SET authentication_string = PASSWORD('password') WHERE User = 'root';
```

2) To make the change take effect, reload the stored user information with the following command:
```
FLUSH PRIVILEGES;
```

**Step 6 - Viewing users**  

MySQL stores the user information in its own database. The name of the database is mysql. Inside that database the user information is in a table, a dataset, named user. If you want to see what users are set up in the MySQL user table, run the following command:
```
SELECT User, Host, authentication_string FROM mysql.user;
```

The following list describes the parts of that command:

* **SELECT** tells MySQL that you are asking for data.  
* **User, Host, authentication_string** tells MySQL what fields you want it to look in. Fields are categories for the data in a table. In this case, you are looking for the username, the host associated with the username, and the encrypted password entry.  
* **FROM mysql.user** tells MySQL to get the data from the mysql database and the user table.  
* A semicolon (;) ends the command.

{% include alert.html type="info" title="Note:" content="All SQL queries end in a semicolon. MySQL does not process a query until you type a semicolon." %}

**User hosts**  

The following example is the output for the preceding query:
```console
mysql> SELECT User, Host, authentication_string FROM mysql.user;
+------------------+-----------+-------------------------------------------+
| User             | Host      | authentication_string                     |
+------------------+-----------+-------------------------------------------+
| root             | localhost |                                           |
| mysql.session    | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| debian-sys-maint | localhost | *D5B38354276160B90596B4C4CC1777EBF7E7D535 |
+------------------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)
```

Users are associated with a host, specifically, the host from which they connect. The root user in this example is defined for localhost, for the IP address of localhost, and the hostname of the server. You usually need to set a user for only one host, the one from which you typically connect.

If you’re running your application on the same computer as the MySQL server, the host that it connects to by default is localhost. Any new users that you create must have localhost in their host field.

If your application connects remotely, the host entry that MySQL looks for is the IP address or DNS hostname of the remote computer (the one from which the client is coming).

**Anonymous users**  

In the example output, one entry has a host value but no username or password. That’s an anonymous user. When a client connects with no username specified, it’s trying to connect as an *anonymous user*.

You usually don’t want any anonymous users, but some MySQL installations include one by default. If you see one, you should either delete the user (refer to the username with empty quotes, like ‘ ‘) or set a password for it.

**Step 6 - Creating a database**  

There is a difference between a database server and a database, even though those terms are often used interchangeably. MySQL is a database server, meaning it tracks databases and controls access to them. The database stores the data, and it is the database that applications are trying to access when they interact with MySQL.

Some applications create a database as part of their setup process, but others require you to create a database yourself and tell the application about it.

To create a database, log in to the `mysql shell` and run the following command, replacing `demodb` with the name of the database that you want to create:
```
CREATE DATABASE demodb;
```

After the database is created, you can verify its creation by running a query to list all databases. The following example shows the query and example output:
```console
SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| demodb             |
| mysql              |
+--------------------+
3 rows in set (0.00 sec)
```

**Step 7 - Adding a database user**  

When applications connect to the database using the root user, they usually have more privileges than they need. You can add users that applications can use to connect to the new database. In the following example, a user named demouser is created.

1) To create a new user, run the following command in the `mysql shell`:

```
INSERT INTO mysql.user (User,Host,authentication_string,ssl_cipher,x509_issuer,x509_subject)
VALUES('demouser','localhost',PASSWORD('demopassword'),'','','');
```

2) When you make changes to the user table in the mysql database, tell MySQL to read the changes by flushing the privileges, as follows:

```
FLUSH PRIVILEGES;
```

3) Verify that the user was created by running a SELECT query again:

```console
mysql> SELECT User, Host, authentication_string FROM mysql.user;
+------------------+-----------+-------------------------------------------+
| User             | Host      | authentication_string                     |
+------------------+-----------+-------------------------------------------+
| root             | localhost |                                           |
| mysql.session    | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| debian-sys-maint | localhost | *D5B38354276160B90596B4C4CC1777EBF7E7D535 |
| demouser         | localhost | *0756A562377EDF6ED3AC45A00B356AAE6D3C6BB6 |
+------------------+-----------+-------------------------------------------+
5 rows in set (0.00 sec)
```

**Step 8 - Granting database user permissions**  

Right after you create a new user, it has no privileges. The user can log in, but can’t be used to make any database changes.

1) Give the user full permissions for your new database by running the following command:
```
GRANT ALL PRIVILEGES ON demodb.* to demouser@localhost;
```

2) Flush the privileges to make the change official by running the following command:
```
FLUSH PRIVILEGES;
```
3) To verify that those privileges are set, run the following command:
```
SHOW GRANTS FOR 'demouser'@'localhost';
```

MySQL returns the commands needed to reproduce that user’s permissions if you were to rebuild the server. `USAGE on \*.\*` means the users gets no privileges on anything by default. That command is overridden by the second command, which is the grant you ran for the new database.

```console
mysql> SHOW GRANTS FOR 'demouser'@'localhost';
+--------------------------------------------------------------+
| Grants for demouser@localhost                                |
+--------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'demouser'@'localhost'                 |
| GRANT ALL PRIVILEGES ON `demodb`.* TO 'demouser'@'localhost' |
+--------------------------------------------------------------+
2 rows in set (0.00 sec)
```

If you’re just creating a database and a user, you are done. The concepts covered here should give you a solid start from which to learn more.


## Install PostgreSQL Server on Ubuntu
-----

PostgreSQL is one of several free popular databases, and it is frequently used for web databases. It was one of the first database management systems to be developed, and it allows users to manage both structured and unstructured data. It can also be used on most major platforms, including Linux-based ones, and it’s fairly simple to import information from other database types using the tool.

PostgreSQL, or Postgres, is a relational database management system that provides an implementation of the SQL querying language. It is a popular choice for many small and large projects and has the advantage of being standards-compliant and having many advanced features like reliable transactions and concurrency without read locks.

This database management engine can be hosted in a number of environments, including virtual, physical and cloud-based environments.

Few features of this DBMS are:
* it is an object-relational database, 
* the data remains secure,
* data retrieval is faster,
* data sharing through dashboards is faster.

This guide demonstrates how to install Postgres on an Ubuntu and also provides instructions for basic database administration.

**Step 1 — Installing PostgreSQL** 

Ubuntu’s default repositories contain Postgres packages, so you can install these using the apt packaging system.

Since this is your first time using `apt` in this session, refresh your local package index. Then, install the Postgres package along with a `-contrib` package that adds some additional utilities and functionality:  
```
sudo apt update
sudo apt install postgresql postgresql-contrib
```
Now that the software is installed, we can go over how it works and how it may be different from similar database management systems you may have used.

**Step 2 — Using PostgreSQL Roles and Databases**  

By default, Postgres uses a concept called “roles” to handle in authentication and authorization. These are, in some ways, similar to regular Unix-style accounts, but Postgres does not distinguish between users and groups and instead prefers the more flexible term “role”.

Upon installation, Postgres is set up to use ident authentication, meaning that it associates Postgres roles with a matching Unix/Linux system account. If a role exists within Postgres, a Unix/Linux username with the same name is able to sign in as that role.

The installation procedure created a user account called postgres that is associated with the default Postgres role. In order to use Postgres, you can log into that account.

There are a few ways to utilize this account to access Postgres:

1) Switching Over to the postgres Account
Switch over to the postgres account on your server by typing:
```
sudo -i -u postgres
```

You can now access a Postgres prompt immediately by typing:
```
psql
```
This will log you into the PostgreSQL prompt, and from here you are free to interact with the database management system right away.

Exit out of the PostgreSQL prompt by typing: `\q`
This will bring you back to the `postgres` Linux command prompt.

2) Accessing a Postgres Prompt Without Switching Accounts
You can also run the command you’d like with the postgres account directly with `sudo`.

For instance, in the last example, you were instructed to get to the Postgres prompt by first switching to the postgres user and then running `psql` to open the Postgres prompt. You could do this in one step by running the single command `psql` as the postgres user with `sudo`, like this:
```
sudo -u postgres psql
```
This will log you directly into Postgres without the intermediary `bash` shell in between.

Again, you can exit the interactive Postgres session by typing: `\q`

Many use cases require more than one Postgres role. Read on to learn how to configure these.

**Step 3 — Creating a New Role**  

Currently, you just have the postgres role configured within the database. You can create new roles from the command line with the `createrole` command. The `--interactive` flag will prompt you for the name of the new role and also ask whether it should have superuser permissions.

If you are logged in as the postgres account, you can create a new user by typing:
```
createuser --interactive
```

If, instead, you prefer to use `sudo` for each command without switching from your normal account, type:
```
sudo -u postgres createuser --interactive
```

The script will prompt you with some choices and, based on your responses, execute the correct Postgres commands to create a user to your specifications.

```console
Enter name of role to add: sammy
Shall the new role be a superuser? (y/n) y
```

You can get more control by passing some additional flags. Check out the options by looking at the `man` page:
```
man createuser
```
Your installation of Postgres now has a new user, but you have not yet added any databases. The next section describes this process.

**Step 4 — Creating a New Database**  

Another assumption that the Postgres authentication system makes by default is that for any role used to log in, that role will have a database with the same name which it can access.

This means that, if the user you created in the last section is called sammy, that role will attempt to connect to a database which is also called “sammy” by default. You can create the appropriate database with the `createdb` command.

If you are logged in as the postgres account, you would type something like:
```
createdb sammy
```

If, instead, you prefer to use `sudo` for each command without switching from your normal account, you would type:
```
sudo -u postgres createdb sammy
```

This flexibility provides multiple paths for creating databases as needed.

**Step 5 — Opening a Postgres Prompt with the New Role**  

To log in with `ident` based authentication, you’ll need a Linux user with the same name as your Postgres role and database.

If you don’t have a matching Linux user available, you can create one with the `adduser` command. You will have to do this from your non-root account with `sudo` privileges (meaning, not logged in as the postgres user):
```
sudo adduser sammy
```

Once this new account is available, you can either switch over and connect to the database by typing:
```
sudo -i -u sammy
psql
```

Or, you can do this inline:
```
sudo -u sammy psql
```
This command will log you in automatically, assuming that all of the components have been properly configured.

If you want your user to connect to a different database, you can do so by specifying the database like this:
```
psql -d postgres
```

Once logged in, you can get check your current connection information by typing:
```
sammy=# \conninfo
```
```console
You are connected to database "sammy" as user "sammy" via socket in "/var/run/postgresql" at port "5432".
```
This is useful if you are connecting to non-default databases or with non-default users.

**Step 6 — Creating and Deleting Tables**

Now that you know how to connect to the PostgreSQL database system, you can learn some basic Postgres management tasks.

First, create a table to store some data. As an example, a table that describes some playground equipment.

The basic syntax for this command is as follows:
```
CREATE TABLE table_name (
    column_name1 col_type (field_length), column_constraints
    column_name2 col_type (field_length),
    column_name3 col_type (field_length)
);
```
As you can see, these commands give the table a name, and then define the columns as well as the column type and the max length of the field data. You can also optionally add table constraints for each column.

For demonstration purposes, create a simple table like this:
```
CREATE TABLE playground (
    equip_id serial PRIMARY KEY,
    type varchar (50) NOT NULL,
    color varchar (25) NOT NULL,
    location varchar(25) check (location in ('north', 'south', 'west', 'east', 'northeast', 'southeast', 'southwest', 'northwest')),
    install_date date
);
```
These commands will create a table that inventories playground equipment. This starts with an equipment ID, which is of the `serial` type. This data type is an auto-incrementing integer. You’ve also given this column the constraint of `primary key` which means that the values must be unique and not null.

For two of the columns (`equip_id` and `install_date`), the commands do not specify a field length. This is because some column types don’t require a set length because the length is implied by the type.

The next two commands create columns for the equipment `type` and `color` respectively, each of which cannot be empty. The command after these creates a `location` column and create a constraint that requires the value to be one of eight possible values. The last command creates a date column that records the date on which you installed the equipment.

You can see your new table by typing: 
```
\d
```
```console
Output
                  List of relations
 Schema |          Name           |   Type   | Owner 
--------+-------------------------+----------+-------
 public | playground              | table    | sammy
 public | playground_equip_id_seq | sequence | sammy
(2 rows)
```
Your playground table is here, but there’s also something called `playground_equip_id_seq` that is of the type `sequence`. This is a representation of the `serial` type which you gave your `equip_id` column. This keeps track of the next number in the sequence and is created automatically for columns of this type.

If you want to see just the table without the sequence, you can type: 
```
\dt
```
```console
Output
          List of relations
 Schema |    Name    | Type  | Owner 
--------+------------+-------+-------
 public | playground | table | sammy
(1 row)
```

**Step 7 — Adding, Querying, and Deleting Data in a Table**

Now that you have a table, you can insert some data into it.

As an example, add a slide and a swing by calling the table you want to add to, naming the columns and then providing data for each column, like this:
```
INSERT INTO playground (type, color, location, install_date) VALUES ('slide', 'blue', 'south', '2017-04-28');
INSERT INTO playground (type, color, location, install_date) VALUES ('swing', 'yellow', 'northwest', '2018-08-16');
```
You should take care when entering the data to avoid a few common hangups. For one, do not wrap the column names in quotation marks, but the column values that you enter do need quotes.

Another thing to keep in mind is that you do not enter a value for the `equip_id` column. This is because this is automatically generated whenever a new row in the table is created.

Retrieve the information you’ve added by typing:
```
SELECT * FROM playground;
```
```console
Output
 equip_id | type  | color  | location  | install_date 
----------+-------+--------+-----------+--------------
        1 | slide | blue   | south     | 2017-04-28
        2 | swing | yellow | northwest | 2018-08-16
(2 rows)
```
Here, you can see that your `equip_id` has been filled in successfully and that all of your other data has been organized correctly.

If the slide on the playground breaks and you have to remove it, you can also remove the row from your table by typing:
```
DELETE FROM playground WHERE type = 'slide';
```
Query the table again:
```
SELECT * FROM playground;
```
```console
Output
 equip_id | type  | color  | location  | install_date 
----------+-------+--------+-----------+--------------
        2 | swing | yellow | northwest | 2018-08-16
(1 row)
```
You notice that your slide is no longer a part of the table.

**Step 8 — Adding and Deleting Columns from a Table**  

After creating a table, you can modify it to add or remove columns relatively easily. Add a column to show the last maintenance visit for each piece of equipment by typing:
```
ALTER TABLE playground ADD last_maint date;
```
If you view your table information again, you will see the new column has been added (but no data has been entered):
```
SELECT * FROM playground;
```
```console
Output
 equip_id | type  | color  | location  | install_date | last_maint 
----------+-------+--------+-----------+--------------+------------
        2 | swing | yellow | northwest | 2018-08-16   | 
(1 row)
```
Deleting a column is just as simple. If you find that your work crew uses a separate tool to keep track of maintenance history, you can delete of the column by typing:
```
ALTER TABLE playground DROP last_maint;
```
This deletes the `last_maint` column and any values found within it, but leaves all the other data intact.

**Step 9 — Updating Data in a Table**  

So far, you’ve learned how to add records to a table and how to delete them, but this tutorial hasn’t yet covered how to modify existing entries.

You can update the values of an existing entry by querying for the record you want and setting the column to the value you wish to use. You can query for the “swing” record (this will match every swing in your table) and change its color to “red”. This could be useful if you gave the swing set a paint job:
```
UPDATE playground SET color = 'red' WHERE type = 'swing';
```
You can verify that the operation was successful by querying the data again:
```
SELECT * FROM playground;
```
```console
Output
 equip_id | type  | color | location  | install_date 
----------+-------+-------+-----------+--------------
        2 | swing | red   | northwest | 2010-08-16
(1 row)
```
As you can see, your slide is now registered as being red.

## Install Apache Cassandra NoSQL DB on Ubuntu
-------

NoSQL database managers are ideal for clusters of data and scalable applications. Among them is Cassandra from the Apache Foundation.

Apache Cassandra is a free and open-source, distributed, wide column store, NoSQL database management system designed to handle large amounts of data across many commodity servers, providing high availability with no single point of failure. Cassandra offers robust support for clusters spanning multiple datacenters, with asynchronous masterless replication allowing low latency operations for all clients.

Few features of this tool are:
* supports replication and multi data center replication,  
* scalability,  
* has Hadoop integration, with MapReduce support,  
* fault-tolerant,  
* introduced the Cassandra Query Language,

 In this tutorial, we will show you how to set up install Cassandra on Ubuntu server.

**Step 1 -Java installation**  

The easiest way to install Apache Cassandra on Ubuntu is by installing the deb package from the official Apache Cassandra repository.

At the time of writing this article, the latest version of Apache Cassandra is 3.11 and requires OpenJDK 8 to be installed on the system.

Java installation is pretty simple, start by updating the package index:
```
sudo apt update
```

Install the OpenJDK package by typing:
```
sudo apt install openjdk-8-jdk
```

Verify the Java installation by running the following command which will print the Java version:
```
java -version
```
```console
openjdk version "1.8.0_222"
OpenJDK Runtime Environment (build 1.8.0_222-8u222-b10-1ubuntu1~18.04.1-b10)
OpenJDK 64-Bit Server VM (build 25.222-b10, mixed mode)
```

Install the apt-transport-https package that necessary to access a repository over HTTPS:
```
sudo apt install apt-transport-https
```

The next step is to add the Apache Cassandra repository.

**Step 2 -  Installing Apache Cassandra**  

After that, we need to add the Cassandra repository to our repository file. 
Import the repository’s GPG using the following wget command:
```
wget -q -O - https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
```

The command above should output OK which means that the key has been successfully imported and packages from this repository will be considered trusted.

Next, add the Cassandra repository to the system by issuing:
```
sudo sh -c 'echo "deb http://www.apache.org/dist/cassandra/debian 311x main" > /etc/apt/sources.list.d/cassandra.list'
```

Once the repository is enabled, update the apt package list and install the latest version of Apache Cassandra by typing:
```
sudo apt update
sudo apt install cassandra
```

**Step 3 - Enabling and Starting Cassandra**  

At the end of the installation, we have to enable the Cassandra service to start automatically when the system boots. In addition, we should start it manually. We can do it with the following commands:
```
sudo systemctl enable cassandra
sudo systemctl start cassandra
```
If you want to make Apache Cassandra automatically start at system boot, run the following command:
```
sudo update-rc.d cassandra defaults
```

**Step 4 - Verifying The Installation**

To check if the installation has been successful, we can check the status of the service. To do this, run the following command:
```
sudo systemctl status cassandra
```
You should see something similar to this:
```console
 cassandra.service - LSB: distributed storage system for structured data
   Loaded: loaded (/etc/init.d/cassandra; generated)
   Active: active (running) since Wed 2019-11-20 15:32:48 UTC; 21min ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 59 (limit: 2361)
   CGroup: /system.slice/cassandra.service
           └─5168 java -Xloggc:/var/log/cassandra/gc.log -ea -XX:+UseThreadPriorities -XX:ThreadPriorityPolicy=42 -XX:+H

Nov 20 15:32:48 db systemd[1]: Starting LSB: distributed storage system for structured data...
```
Also, you can use the nodetool program to show the status of Apache Cassandra on current node:
```
nodetool status
```
You should see something similar to this:
```console
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address    Load       Tokens       Owns (effective)  Host ID                               Rack
UN  127.0.0.1  103.54 KiB  256          100.0%            7017681d-0d9b-46af-b578-f7888ddd0795  rack1
```

Congratulations, at this point you have Apache Cassandra installed on your Ubuntu server.

**Step 5 - Configuring Apache Cassandra**  

Apache Cassandra data is stored in the /var/lib/cassandra directory, configuration files are located in /etc/cassandra and Java start-up options can be configured in the /etc/default/cassandra file.

By default, Cassandra is configured to listen on localhost only. If the client connecting to the database is also running on the same host you don’t need to change the default configuration file.

To interact with Cassandra through CQL (the Cassandra Query Language) you can use a command line utility named cqlsh that is shipped with the Cassandra package.
```
cqlsh
```
```console
OUTPUT
Connected to Linuxize Cluster at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 3.9 | CQL spec 3.4.2 | Native protocol v4]
Use HELP for help.
cqlsh>
```
For now, just type `exit` and then press `ENTER` to quit the cqlsh shell.

**Step 6 - Renaming Apache Cassandra Cluster**  

By default, the Cassandra cluster is named “Test Cluster”. If you want to change it follow the steps bellow:

1) Login to the Cassandra CQL terminal with `cqlsh`:
```
cqlsh
```

Run the following command to change the cluster name to “Linuxize Cluster”:
```
UPDATE system.local SET cluster_name = 'Linuxize Cluster' WHERE KEY = 'local';
```
Change “Linuxize Cluster” with your desired name. Once done type exit to exit the console.

2) Edit the `cassandra.yaml` configuration file and enter your new cluster name.
```
vi /etc/cassandra/cassandra.yaml
```
```
cluster_name: 'Linuxize Cluster'
```

3) Run the following command to clear the system cache:
```
nodetool flush system
```

4) Finally restart the Cassandra service:
```
sudo systemctl restart cassandra
```

**Step 7 - Stopping Apache Cassandra**  

If you want to stop Apache Cassandra, execute the following command:
```
sudo service cassandra stop
```

Cassandra is a very powerful application, that has many configuration options. However, we have observed the most basic ones that will help you start working with it right away. If you want to learn more, we recommend checking the [official documentation](http://cassandra.apache.org/doc/latest/)








