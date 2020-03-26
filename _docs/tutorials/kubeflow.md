---
title: Running ML pipeline using Kubeflow on Kubernetes
description: How to deploy Kubeflow on kubernetes and compile, train and serve ML pipeline.
tags: [ featured, tutorial, kubernetes, kubeflow ]
# permalink: /new-Linux-VM-using-Ventus/
---
# Running ML pipeline using Kubeflow on Kubernetes
{: .no_toc }
---

**Kubeflow** - The Machine Learning Toolkit for Kubernetes" content="The Kubeflow project is dedicated to making deployments of machine learning (ML) workflows on Kubernetes simple, portable and scalable.

In this tutorial we will use Kubernetes and Kubeflow in order to compile, train and serve model of machine learning.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Kubernetes cluster
---

Create new cluster using this article: [Kubernetes Cluster](https://ventuscloud.eu/docs/Kubernetes/Kubernetes%20Cluster)   

Use next parameters for your cluster:  
  - `Master count`: 1  
  - `Node count`: 1  
  - `Docker volume size (GB)`: 100  
  - `Node flavor`: VC-16  
  - `Master node flavor`: VC-4  



## Get access to your cluster using cli
---

Now you can getting access to your cluster using this article [Access to Kubernetes Cluster using CLI](https://ventuscloud.eu/docs/Kubernetes/access-by-cli)   



## Deploy Kubeflow
---

Follow these steps to deploy Kubeflow:

1) Download and install `kfctl` release from the <a href ="https://github.com/kubeflow/kubeflow/releases/">Kubeflow releases page</a>: 
```
wget https://github.com/kubeflow/kubeflow/releases/download/v0.7.0/kfctl_v0.7.0_linux.tar.gz
```
```
tar -xvf kfctl_v0.7.0_linux.tar.gz
```
```
sudo cp kfctl /usr/bin/
```

2) Run the following commands to set up and deploy Kubeflow:
- Set the name of a directory where you want Kubeflow configurations to be stored. This directory is created when you run kfctl init:   
```
export PATH=$PATH:"<path-to-kfctl>"
```

- Specify path to kfctl config file which will be used for kubeflow installation:    
``` 
export CONFIG_URI="https://raw.githubusercontent.com/kubeflow/manifests/dc04ff600cee722d93cf80d413aa73ddd8387f1f/kfdef/kfctl_existing_arrikto.0.7.0.yaml"
```

- Set KF_NAME to the name of your Kubeflow deployment (For example, your deployment name can be `'my-kubeflow'`):    
```
export KF_NAME=<your choice of name for the Kubeflow deployment>
```

- Set the path to the base directory where you want to store one or more Kubeflow deployments (The path should be absolute):    
```
export BASE_DIR=<path to a base directory>
```

- Set the Kubeflow application directory for this deployment:    
```
export KF_DIR=${BASE_DIR}/${KF_NAME}
```

- Create directory and download the config file with default login credentials:    
  - Create directory:    
```
mkdir -p ${KF_DIR}
```
```
cd ${KF_DIR}
```

  - Download the config file:     
```
wget -O kfctl_existing_arrikto.yaml $CONFIG_URI
```
```
export CONFIG_FILE=${KF_DIR}/kfctl_existing_arrikto.yaml
```

Now, you have `kfctl_existing_arrikto.yaml` file with default user credentials: `admin@kubeflow.org:12341234`.
You can chage credentials using `vi kfctl_existing_arrikto.yaml` command and overwrite it.

- Use command `kfctl apply -V -f ${CONFIG_FILE}` to deploy Kubeflow.    

3) Run next command to check that all pods are running:
```
kubectl get pods --all-namespaces
```

![](../../assets/img/tutorials/tekton-pipelines/verify_2.png)

## Accsessing Kubeflow
---
**Log in as a static user**

After deploying Kubeflow, the Kubeflow dashboard is available at the Istio Gateway IP. To get the Istio Gateway IP, run:
```
kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Get the IP and open it in a browser: `https://<LoadBalancerIP address>/.`

Enter the credentials you specified in `KUBEFLOW_USER_EMAIL`, `KUBEFLOW_PASSWORD` and access the Kubeflow dashboard!  

**Expose with a LoadBalancer**

To expose Kubeflow with a LoadBalancer Service, just change the type of the `istio-ingressgateway` Service to `LoadBalancer`.

```
kubectl patch service -n istio-system istio-ingressgateway -p '{"spec": {"type": "LoadBalancer"}}'
```

After that, get the LoadBalancer’s IP or Hostname from its status and create the necessary certificate.

```
kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0]}'
```

Create the Certificate with cert-manager:
```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: istio-ingressgateway-certs
  namespace: istio-system
spec:
  commonName: istio-ingressgateway.istio-system.svc
  # Use ipAddresses if your LoadBalancer issues an IP
  ipAddresses:
  - <LoadBalancer IP>
  # Use dnsNames if your LoadBalancer issues a hostname (eg on AWS)
  dnsNames:
  - <LoadBalancer HostName>
  isCA: true
  issuerRef:
    kind: ClusterIssuer
    name: kubeflow-self-signing-issuer
  secretName: istio-ingressgateway-certs
```
After applying the above Certificate, cert-manager will generate the TLS certificate inside the istio-ingressgateway-certs secrets. The istio-ingressgateway-certs secret is mounted on the istio-ingressgateway deployment and used to serve HTTPS.

Navigate to `https://<LoadBalancer Address>/` and start using Kubeflow.

## Compilation of a mnist pipelines
---

{% include alert.html type="info" title="Requirements:" content="Next packages should be installed on your client for the next steps: python3, pip3, docker." %}

Run next commands to compile pipeline:

```
git clone https://github.com/ventus-ag/kubeflow.git
cd kubeflow/samples/mnist-pipelines/deploy-service
docker build -t ACCOUNT/NAME .
docker login
docker push ACCOUNT/NAME
cd ..
```
  -  Open file `mnist_pipeline.py` and change the docker image name that you specified earlier in line: 
```
image='tens/deploy-service:latest'
```

     ![](../../assets/img/tutorials/tekton-pipelines/docker_build_update.png)

```
pip3 install -r requirements.txt --upgrade
python3 mnist_pipeline.py
```

As result, you must got file `mnist_pipeline.py.tar.gz` in your folder

![](../../assets/img/tutorials/tekton-pipelines/Pipeline_file.png)


## Upload through the UI

Now that you have the compiled pipeline file, you can upload it through the Kubeflow Pipelines UI. Simply select the **"Upload pipeline"** button:

![](../../assets/img/tutorials/tekton-pipelines/Upload_pipeline.png)

Upload your file and give it a name:

![](../../assets/img/tutorials/tekton-pipelines/Upload_and_giving_a_name.png)

## Run the Pipeline

After clicking on the newly created pipeline, you should be presented with an overview of the pipeline graph. When you're ready, select the "Create Run" button to launch the pipeline:

![](../../assets/img/tutorials/tekton-pipelines/Create_run.png)

Fill out the information required for the run, and press "Start" when you are ready.
 - The `model-export-dir` field must be `/mnt/export`
 - For `pvc-name` field you must create new pvc in your cluster. You can use next `pvc.yaml` file to do that:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  finalizers:
  - kubernetes.io/pvc-protection
  name: local-storage
  namespace: kubeflow
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
``` 
 - Apply this file to your cluster:    
```
kubectl apply -f pvc.yaml
```

 - Specify name from file you created into `pvc-name` filed.

![](../../assets/img/tutorials/tekton-pipelines/Run_details.png)

![](../../assets/img/tutorials/tekton-pipelines/Run_type.png)

**Review experiment**

After clicking on the newly created Run, you should see the pipeline run through the `train`, `serve`, and `web-ui` components. Click on any component to see its logs. When the pipeline is complete, look at the logs for the `web-ui` component to find the IP address created for the MNIST web interface:

![](../../assets/img/tutorials/tekton-pipelines/Experiment.png)

Use IP you received in previous step and open web UI page with experiment's result:

![](../../assets/img/tutorials/tekton-pipelines/Experiment_result.png)

Congratulations! You successfully finished this tutorial.

Let's re-cap what we've done:
- Created new kubernetes cluster for Kubeflow.
- Deployed Kubeflow.
- Compiled mnist pipeline example.
- Uploaded pipeline to Kubeflow.
- Created and ran new experiment.
- As a result trained and served mnist model using Kubeflow in kubernetes.




















