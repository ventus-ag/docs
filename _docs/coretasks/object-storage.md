---
title: Ventus Cloud Object Storage
description: What is Ventus Cloud Object Storage, how we can use it
tags: [ featured, coretasks ]
# permalink: /Object-Storage/
---
# Object Storage
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Introduction to Object Storage

The Ventus Cloud Object Storage makes it possible to store practically limitless amounts of data, simply and cost effectively. It provides a fully distributed, API-accessible storage platform that can be integrated directly into applications or used for backup, archiving, and data retention.

It's built for scale and optimized for durability, availability, and concurrency across the entire data set. Swift is ideal for storing unstructured data that can grow without bound.

An Object Storage API differs from a conventional filesystem: instead of directories and files, you manipulate containers where you store objects. A container can hold millions of objects.

There is no notion of hierarchy with containers: you cannot nest a container within another, however you can emulate a nested folder structure with a naming convention for your objects. 

## Configure Object Storage with the Python API

1) Python support is provided through a fork of the boto3 library with features to make the most of Ventus Cloud Object Storage. To install it 
run the command bellow
```
pip install boto
```
2) To connect to COS, a client is created and configured using credential information. These values can also be automatically sourced from a credentials file or from environment variables. To get this information type:
```
openstack --os-interface public ec2 credentials list
```
After this command you can see your credential information, remember the access_key, secret_key, because you need this information in the following steps.

3) Next you need to create new file (ex. s3.py ) with the next phyton code:
```
vi s3.py
```
```
import boto
import boto.s3.connection
access_key = 'your_access_key'
secret_key = 'your_secret_key'

conn = boto.connect_s3(
        aws_access_key_id = access_key,
        aws_secret_access_key = secret_key,
        host = 'cloud.vstack.ga',
        port = 8080,
        #is_secure=False,               # uncomment if you are not using ssl
        calling_format = boto.s3.connection.OrdinaryCallingFormat(),
        )

bucket = conn.create_bucket('my_new_bucket')

for bucket in conn.get_all_buckets():
        print "{name}\t{created}".format(
                name = bucket.name,
                created = bucket.creation_date,
        )

```
Remember to replace the ‘your_access_key’ and 'your_secret_key' part with your credential information.

Then press `Esc :wq`, and `Enter` to save the changes.

4) To execute your created "s3.py" file type the next:

```
python s3.py
```
If the execution was completed without a problem, you should see the next output, what meens that we have already created the new bucket, which name is my_new_bucket :
```
my_new_bucket  2019-10-09T08:13:19.071Z
```

So, as we can see, by using phyton code, we have just:

- created a connection so that you can interact with the server
- created a new bucket called my_new_bucket
- got a list of Buckets that you own and printed out the bucket name and creation date of each bucket.











