---
title: Access to the Kubernetes Cluster using CLI
description: How to get access to the Kubernetes Cluster using CLI 
tags: [ featured, coretasks ]
# permalink: /access-by-cli/
---
# Access to Kubernetes Cluster using CLI
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Get access from Ubuntu VM to Kubernetes Cluster using CLI

For this coretask we suppose that in Ventus cloud we've already created:
- the instance, from which we need to get access to the Kubernetes Cluster API, with the next parameters:
    * Name: Test_Inst
    * Image: ubuntu-1804-bionic
   
- the Kubernetes Cluster, with the next parameters:
    * Name: Test_Cluster

- API User, which has the next detailes and just loaded "openrc" file:
    * name: Test_User
    * ID=b38e8ed15b4f49cbad07171122334455

To get the access to your Kubernetes cluster using CLI follow the next steps:

1) Loggin to your Instance from which we need to get access to the Kubernetes Cluster API

2) Update Ubuntu package sources by running the following command:

```
sudo apt update
```

3) Install Kubernetes Python Client by running the next command:

```
sudo apt install python-pip
```

4) Install openstack cli tool by running two next commands one by one: 

```
sudo pip install python-openstackclient
sudo pip install python-magnumclient
```

5) Place "openrc" file to your server Test_Inst 

```
vi openrc
```

Сheck that there were indicated the correct OS_USERNAME and  OS_PROJECT_ID:

```
export OS_ENDPOINT_TYPE=publicURL
export OS_INTERFACE=publicURL

# COMMON OPENSTACK ENVS
export OS_USERNAME=Test_User
export OS_PROJECT_ID=b38e8ed15b4f49cbad07171122334455
echo "Please enter your OpenStack password as user $OS_USERNAME: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT
export OS_AUTH_URL=http://172.29.236.10:5000/v3
export OS_NO_CACHE=1
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_REGION_NAME=RegionOne

# For openstackclient
export OS_IDENTITY_API_VERSION=3
export OS_AUTH_VERSION=3
```
Then press `Esc :wq`, and `Enter` to save the changes.

6) Execute "openrc" file starting with dot:

```
. openrc
```

7) Provide password of created API user and hit `enter` - this password will be used to authenticate you in the Ventus Cloud.

8) Run next command to get a list of all clusters:

```
openstack coe cluster list
```

9) Run next command to get kubeconfig file for your cluster:

```
mkdir ~/Test_Cluster
openstack coe cluster config --dir ~/Test_Cluster Test_Cluster
```

10) Export path to created config for as KUBECONFIG env variable:

```
export KUBECONFIG=~/Test_Cluster/config
```

11) Install kubectl by running the next command:

```
snap install kubectl --classic
```

12) Run next command to test that you have access to the cluster and all pods are running:

```
kubectl get nodes
kubectl get pods --all-namespaces
```

## Get access from Centos VM to Kubernetes Cluster using CLI

For this coretask we suppose that in Ventus cloud we've already created:
- the instance, from which we need to get access to the Kubernetes Cluster API, with the next parameters:
    * Name: Test_Inst
    * Image: CentOS-7
   
- the Kubernetes Cluster, with the next parameters:
    * Name: Test_Cluster

- API User, which has the next detailes and just loaded "openrc" file:
    * name: Test_User
    * ID=b38e8ed15b4f49cbad07171122334455

To get the access to your Kubernetes cluster using CLI follow the next steps:

1) Login to your Instance from which we need to get access to the Kubernetes Cluster API

2) Add the CentOS 7 RDO repository by using the following command:

```
yum install -y https://www.rdoproject.org/repos/rdo-release.rpm
```

3) Install openstack cli tool by running two next commands one by one: 

```
yum install python-openstackclient
yum install python-magnumclient
```

4) Place "openrc" file to your server Test_Inst 

```
vi openrc
```

Сheck that there were indicated the correct OS_USERNAME and  OS_PROJECT_ID:

```
export OS_ENDPOINT_TYPE=publicURL
export OS_INTERFACE=publicURL

# COMMON OPENSTACK ENVS
export OS_USERNAME=Test_User
export OS_PROJECT_ID=b38e8ed15b4f49cbad07171122334455
echo "Please enter your OpenStack password as user $OS_USERNAME: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT
export OS_AUTH_URL=http://172.29.236.10:5000/v3
export OS_NO_CACHE=1
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_REGION_NAME=RegionOne

# For openstackclient
export OS_IDENTITY_API_VERSION=3
export OS_AUTH_VERSION=3
```
Then press `Esc :wq`, and `Enter` to save the changes.

5) Execute "openrc" file starting with dot:

```
. openrc
```

6) Provide password of created API user and hit `enter` - this password will be used to authenticate you in the Ventus Cloud.

7) Run next command to get a list of all clusters:

```
openstack coe cluster list
```

8) Run next command to get kubeconfig file for your cluster:

```
mkdir ~/Test_Cluster

openstack coe cluster config --dir ~/Test_Cluster Test_Cluster
```

9) Export path to created config for as KUBECONFIG env variable:

```
export KUBECONFIG=~/Test_Cluster/config
```

10) Install the latest release of  kubectl, make the kubectl binary executable and move the binary in to your PATH by running the next commands:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl
```

11) Run next command to test that you have access to the cluster and all pods are running:

```
kubectl get nodes
kubectl get pods --all-namespaces
```















