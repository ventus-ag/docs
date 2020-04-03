---
title: CI/CD using Knative and Tekton Pipelines
description: How to run build and deploy cloud native services
tags: [ featured, tutorial ]
# permalink: /new-Linux-VM-using-Ventus/
---
# CI/CD using Knative and Tekton Pipelines
{: .no_toc }
---

**Tekton** is a Kubernetes-native pipeline resource" content="The Tekton Pipelines project provides Kubernetes-style resources for declaring CI/CD-style pipelines.

**Knative** is a Kubernetes-based platform to deploy and manage modern serverless workloads. Knative components build on top of Kubernetes, abstracting away the complex details and enabling developers to focus on what matters.

In this tutorial we will use Kubernetes, Knative, Tekton Pipelines, Dashboard and Webhooks extension to configure CI?CD with your GitHub repository.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Create new Kubernetes cluster
---

Create new cluster using this article: [Kubernetes Cluster](https://ventuscloud.eu/docs/kubernetes/kubernetes-cluster)   

Use next parameters for your cluster:
  - `Master count`: 1  
  - `Node count`: 1  
  - `Docker volume size (GB)`: 100  
  - `Node flavor`: VC-4  
  - `Master node flavor`: VC-2  

## Get access to your cluster using cli
---

Now you can getting access to your cluster using this article [Access to Kubernetes Cluster using CLI](https://ventuscloud.eu/docs/kubernetes/access-by-cli)   


## Deploy Tekton Pipelines, Dashboard and Webhooks extension
---
{% include alert.html type="info" title="Note:" content="All installations are done via cli using kubectl. Kubectl is a command line interface for running commands against Kubernetes clusters." %}

{% include alert.html type="info" title="Tip: install kubectl" content="To install kubectl use kubernetes official docs: https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux" %}

1) Install latest Tekton Pipelines (0.5.2 was latest at the time of writing this tutorial):
<br />`kubectl apply --filename https://storage.googleapis.com/tekton-releases/latest/release.yaml`

![](../../assets/img/tutorials/tekton-pipelines/deploy_pipelines_1.png)

2) Verify that all Tekton Pipelines pods are running:
<br />`kubectl get pods -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/deploy_pipelines_2.png)

## Deploy Tekton Dashboard
---

1) Install Tekton Dashboard (0.1.1 was latest at the time of writing this tutorial):
<br />`kubectl apply --filename https://github.com/tektoncd/dashboard/releases/download/v0.1.1/release.yaml`

![](../../assets/img/tutorials/tekton-pipelines/deploy_dashboard_1.png)

2) Verify that Tekton Dashboard pod is running:
<br />`kubectl get pods -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/deploy_dashboard_2.png)

3) Change service's type of Tekton Dashboard from "ClusterIP" to "LoadBalancer" to receive external IP and make it reachible externally:
<br />`kubectl patch service tekton-dashboard -n tekton-pipelines -p '{"spec": {"type": "LoadBalancer"}}'`

![](../../assets/img/tutorials/tekton-pipelines/deploy_dashboard_3.png)

4) Wait until external IP will be assigned to your service (usually less than a minute):
<br />`kubectl get svc -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/deploy_dashboard_4.png)

5) Get your external IP address and port and open up dashboard in your browser:
<br />`http://188.40.161.51:9097`

![](../../assets/img/tutorials/tekton-pipelines/deploy_dashboard_5.png)

## Deploy Tekton Webhooks extension
---

{% include alert.html type="info" title="Important Note:" content="At the time of writing this tutorial Tekton Webhooks extension was a part of 'experimental' repository and was under development. Be aware that some steps might change in future. Here is the link to installation instructions that we would cover next: https://github.com/tektoncd/experimental/blob/master/webhooks-extension/README.md" %}

1) Install prerequisites:
  - First prerequisites are Tekton Pipelines and Tekton Dashboard and we already installed them.
  - Install Istio following https://knative.dev/docs/install/installing-istio/ or you can run this script https://raw.githubusercontent.com/tektoncd/experimental/master/webhooks-extension/scripts/install_istio.sh like this: `./install_istio.sh 1.1.7` *(Istio version 1.1.7 is recommended).*
    - If you've chosen to run script, then you need to install Helm (https://github.com/helm/helm/releases) first:
      - `wget https://raw.githubusercontent.com/helm/helm/master/scripts/get`
      - `chmod +x ./get`
      - `./get`
      - `helm init`

      ![](../../assets/img/tutorials/tekton-pipelines/deploy_helm_1.png)
    - Continue with script:
      - `wget https://raw.githubusercontent.com/tektoncd/experimental/master/webhooks-extension/scripts/install_istio.sh`
      - `chmod +x ./install_istio.sh`
      - `./install_istio.sh 1.1.7`

      ![](../../assets/img/tutorials/tekton-pipelines/deploy_istio_1.png)
  - Verify that necessary Istio components (we need only two: istio-ingressgateway and istio-pilot) are running:
  <br />`kubectl get pods -n istio-system`

  ![](../../assets/img/tutorials/tekton-pipelines/deploy_istio_1.png)
  - Install Knative Eventing, Eventing Sources & Serving following https://knative.dev/docs/install/index.html or you can run this script https://raw.githubusercontent.com/tektoncd/experimental/master/webhooks-extension/scripts/install_knative.sh like this: `./install_knative.sh v0.6.0` *(Knative version 0.6.0 is strongly recommended).*
    - If you've chosen to run script, use next steps:
      - `wget https://raw.githubusercontent.com/tektoncd/experimental/master/webhooks-extension/scripts/install_knative.sh`
      - `chmod +x ./install_knative.sh`
      - `./install_knative.sh v0.6.0`

      ![](../../assets/img/tutorials/tekton-pipelines/deploy_knative_1.png)
      - `kubectl apply -f https://github.com/knative/eventing-sources/releases/download/v0.6.0/eventing-sources.yaml`
    - Verify that necessary Knative components are running:
    <br />`kubectl get pods --all-namespaces | grep knative`

    ![](../../assets/img/tutorials/tekton-pipelines/deploy_knative_2.png)

2) Run next command to install the Tekton Webhooks Extension and its dependencies:
  - `kubectl apply --filename https://github.com/tektoncd/dashboard/releases/download/v0.1.1/webhooks-extension_release.yaml -n tekton-pipelines`
  
  ![](../../assets/img/tutorials/tekton-pipelines/deploy_webhooks_1.png)
  - Verify that all pods are running:
  <br />`kubectl get pods -n tekton-pipelines`
  
  ![](../../assets/img/tutorials/tekton-pipelines/deploy_webhooks_2.png)
  - Verify that "Webhooks" section is added to your Tekton Dashboard:
  
  ![](../../assets/img/tutorials/tekton-pipelines/deploy_webhooks_3.png)

## Configure Tekton objects
---

In this part of tutorial we will configure all necessary Tekton object and GitHub and Docker repositories. 

Let's start with pre-requisites. First will be GitHub repo:

1) Login to your account (or create new one).
2) Use existing repository which you want to test or use fork version of mine:
  - Go to <a href="https://github.com/TenSt/simple-app">https://github.com/TenSt/simple-app</a> (this is also a fork of mchmarny/simple-app, but with my changes).
  
  ![](../../assets/img/tutorials/tekton-pipelines/github_repo_1.png)
  - Click on the "Fork" button at the top right corner and select your account as a destination where to create new fork.
  - As a result you should have your own repository with unique full name, like: https://github.com/TenSt/simple-app
  
  ![](../../assets/img/tutorials/tekton-pipelines/github_repo_2.png)

Next we need to create a token which we will use for authentication from our Tekton Webhooks extension to GitHub repos:

1) Click on your profile button at the top right corner and go to "Settings".

![](../../assets/img/tutorials/tekton-pipelines/github_token_1.png)

2) Go to "Developer settings" at the bottom of "Personal settings" menu and then "Personal access tokens".

3) Click "Generate new token" button.

![](../../assets/img/tutorials/tekton-pipelines/github_token_2.png)

4) Give it a name in the "Note" field.

5) Select only "admin:repo_hook" checkbox - this will give full access to repo webhooks (which we will use later on).

6) Hit "Generate token" button at the very bottom.

7) You will return back to the "Personal access tokens" page and will see next message: "Make sure to copy your new personal access token now. You won’t be able to see it again!" Go ahead and save your new token somewhere as GitHub will show it only once (if you'll lose it, you will need to generate new one). We will use it later in this tutorial.

Now let's configure your Docker repository (I'm using Docker Hub - https://hub.docker.com).
1) Login to your Docker registry.

2) Create new repository with the same name as your GitHub repository (e.g. simple-app).

3) Make sure that it is public (it is posible to use private Docker repos, but this tutorial doesn't cover it).

Great! We've finished with configuring our repos. As a result you should have:
  - GitHub repo with application and Dockerfile in the root directory which we will deploy
  - GitHub token with admin access to repo webhooks
  - Public docker repository where we will store our images

Next step is to configure domain for Knative which we deployed previosly. To integrate properly with other services it is strongly recommended to configure your own domain (which is accessible from Internet) in Knative who will use it to properly route requests to different services in your Kubernetes cluster.

1) In your cli run next command to get external IP address of istio-ingressgateway service:
<br />`kubectl get svc istio-ingressgateway -n istio-system`

![](../../assets/img/tutorials/tekton-pipelines/knative_domain_1.png)

2) Configure wildcard DNS record of type "A" which points to this EXTERNAL-IP address from previous command. In my case: *.tekton-tutorial.tenst.ml points to 188.40.161.33.

3) Create new yaml file called `knative-domain-config.yaml`:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: config-domain
  namespace: knative-serving
data:
  # Default domain, provided without selector.
  tekton-tutorial.tenst.ml: |
```

4) Change "tekton-tutorial.tenst.ml" to your domain which you configured previously.

5) Apply it to your cluster:
<br />`kubectl apply -f knative-domain-config.yaml`

![](../../assets/img/tutorials/tekton-pipelines/knative_domain_2.png)

Now all requests to *.tekton-tutorial.tenst.ml will route to our istio-ingressgateway service which will route it to appropriate services in our Kubernetes cluster.

One more thing needed to be configured is Knative event source - GitHubSource:

1) Create file `gitHubSource.yaml` - it will create Knative GitHubSource which is pointed to GitHub repo:
```yaml
apiVersion: sources.eventing.knative.dev/v1alpha1
kind: GitHubSource
metadata:
  name: githubsourcesample
  namespaces: tekton-pipelines
spec:
  eventTypes:
    - push
  ownerAndRepository: TenSt/simple-app
  accessToken:
    secretKeyRef:
      name: tenst-github-token
      key: accessToken
  secretToken:
    secretKeyRef:
      name: tenst-github-token
      key: secretToken
  sink:
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    name: webhooks-extension-sink
```

2) Fill in next fields:
  - ownerAndRepository - change it to your account/reponame
  - accessToken:secretKeyRef:name - change it to some unique name
  - secretToken:secretKeyRef:name - should be the same as previous one

3) Apply it to your cluster:
<br />`kubectl apply -f gitHubSource.yaml -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/knative_github_sources_1.png)

Let's move on and configure create new Tekton tasks, resources and pipeline:

1) Apply buildah task - it will be used to build our app:
<br />`kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/master/buildah/buildah.yaml -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/tekton_tasks_1.png)

2) Create file `knctl-role.yaml` - it will create and bind necessary permissions for already existing service account:
```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: knctl-deployer
  namespace: tekton-pipelines
rules:
  - apiGroups: ["serving.knative.dev"]
    resources: ["*"]
    verbs: ["get", "list", "create", "update", "delete", "patch", "watch"]
  - apiGroups: [""]
    resources: ["namespaces"]
    verbs: ["get", "list", "create", "update", "delete", "patch", "watch"]
---

apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: knctl-deployer-binding
subjects:
- kind: ServiceAccount
  name: tekton-webhooks-extension
  namespace: tekton-pipelines
roleRef:
  kind: ClusterRole
  name: knctl-deployer
  apiGroup: rbac.authorization.k8s.io
```

It is composed with two parts:
  - Creating ClusterRole in our Kubernetes cluster which have full access to all resources in "serving.knative.dev" and "namespaces" resource.
  - Creating ClusterRoleBinding which will bind new role to existing tekton-webhooks-extension account (under which our pipelines will run).

3) Apply it to your cluster:
<br />`kubectl apply -f knctl-role.yaml -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/tekton_tasks_2.png)

4) Create file `knctl-task.yaml` - it will create new taks named `knctl-deploy` which will be used to deploy our app to Knative in our cluster:
```yaml
apiVersion: tekton.dev/v1alpha1
kind: Task
metadata:
  name: knctl-deploy
spec:
  inputs:
    params:
    - name: service
      description: Name of the service to deploy
    resources:
    - name: image
      type: image
    - name: git
      type: git
  steps:
  # the first step is required as knctl doesn't support inCluster configuration.
  - name: kubeconfig
    image: gcr.io/cloud-builders/kubectl # it is huge
    command: ["/bin/bash"]
    args:
    - -c
    - mkdir -p /builder/home/.kube; kubectl config view > /builder/home/.kube/config
  - name: cut
    image: golang:latest
    command: ["/bin/bash"]
    args:
    - -c
    - echo ${inputs.resources.git.url} | cut -d "/" -f 4 | tr "[:upper:]" "[:lower:]" > /workspace/account
  - name: namespace
    image: gcr.io/cloud-builders/kubectl # it is huge
    command: ["/bin/bash"]
    args:
    - -c
    - kubectl create ns `cat /workspace/account` --dry-run=true -o yaml | kubectl apply -f -
  - name: rollout
    image: tens/knctl
    command: ["/bin/bash"]
    args:
    - -c
    - knctl deploy --service ${inputs.params.service} --image ${inputs.resources.image.url} --namespace `cat /workspace/account`
```

It is composed with 4 parts:
  - Get kubeconfig from cluster and put in into "/builder/home/.kube/config" - this is required as knctl doesn't support inCluster configuration.
  - Get Account name out of GitHub repository URL
  - Create new namespace the same as GitHub
  - Deploy our app to Knative

5) Apply it to your cluster:
<br />`kubectl apply -f knctl-task.yaml -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/tekton_tasks_3.png)

6) Create file `build-and-deploy-pipeline.yaml` - it will create new pipeline named `build-and-deploy-pipeline` which will be used to deploy our app to Knative in our cluster:
```yaml
apiVersion: tekton.dev/v1alpha1
kind: Pipeline
metadata:
  name: pipeline-build-and-deploy
spec:
  params:
    - name: repository-name
      type: string
      description: repository name which also will be used as namespace
    - name: image-tag
      type: string
      description: commit id
  resources:
    - name: git-source
      type: git
    - name: docker-image
      type: image
  tasks:
    - name: build-buildah
      taskRef:
        name: buildah
      params:
        - name: DOCKERFILE
          value: ./Dockerfile
      resources:
        inputs:
          - name: source
            resource: git-source
        outputs:
          - name: image
            resource: docker-image
    - name: knctl-deploy
      taskRef:
        name: knctl-deploy
      params:
        - name: service
          value: $(params.repository-name)
      resources:
        inputs:
          - name: image
            resource: docker-image
            from:
              - build-buildah
          - name: git
            resource: git-source
```

It is composed with 2 tasks which we created earlier:
  - buildah - to build and push image with our app.
  - kntcl-deploy - to deploy newly created image to Knative in our cluster.

7) Apply it to your cluster:
<br />`kubectl apply -f build-and-deploy-pipeline.yaml -n tekton-pipelines`

![](../../assets/img/tutorials/tekton-pipelines/tekton_tasks_4.png)

After we applied last steps we now have:
  - Tasks for building and deploying
  - Roles and permissions
  - Pipeline

Last but not least part of configuration is creating secrets for GitHub and Docker registry - they will be used by Webhooks extension to clone your GitHub repo and push new image to your Docker registry.

1) Login to your Tekton Dashboard and select "Secrets" from the navigational menu.

2) Hit "Add Secret" button and provide all data:
  - Name: unique name (e.g. tenst-github)
  - Namespace: select "tekton-pipelines" from the drop-down list
  - Access To: Git Server
  - Username: username of GitHub account
  - Password/Token: password of GitHub account
  - Service Account: select "tekton-webhooks-extension"
  - Server URL: leave the first field as "tekton.dev/git-0" and put "https://github.com" in second
  
  ![](../../assets/img/tutorials/tekton-pipelines/tekton_secrets_1.png)

3) Hit "Submit" button.

4) You will see that new secret was added. Now hit "Add Secret" once more to add it for Docker registry:
  - Name: unique name (e.g. tens-docker)
  - Namespace: select "tekton-pipelines" from the drop-down list
  - Access To: Docker Registry
  - Username: username of Docker registry account
  - Password/Token: password of Docker registry account
  - Service Account: select "tekton-webhooks-extension"
  - Server URL: leave the first field as "tekton.dev/docker-0" and put link to your Docker registry in second (use "https://index.docker.io/v1/" if you're using Docker Hub)
  
  ![](../../assets/img/tutorials/tekton-pipelines/tekton_secrets_2.png)

5) Hit "Submit" button.

6) You will see that new secret was added.

![](../../assets/img/tutorials/tekton-pipelines/tekton_secrets_2.png)

## Create new webhook
---

Now we've come to the final section of this tutorial - creating new webhook and observing how our application will be automatically builded and deployed to our Knative in Kubernetes cluster.

1) Login to your Tekton Dashboard and select "Webhooks" from the navigational menu.

2) On the "Create Webhook" page fill all necessary data:
  - Name: unique name of the webhook (e.g. tenst-simple-app-webhook)
  - Repository URL: full URL to your GitHub repo
  - Access Token: 
    - Hit "+" button
    - Fill unique name for token (e.g. tenst-github-token)
    - Paste token which we created previously
    - Hit "Create" button
    ![](../../assets/img/tutorials/tekton-pipelines/webhook_create_1.png)
  - Namespace: tekton-pipelines
  - Pipeline: build-and-deploy-pipeline
  - Service Account: tekton-webhook-extension
  - Docker Registry: name of your account in the Docker Registry (e.g. tens)
  - Hit "Create" button

3) Review that webhook was successfully created.

![](../../assets/img/tutorials/tekton-pipelines/webhook_create_2.png)

Now we are ready to test it!

1) Do some changes to your GitHub repo and commit them. This will create new `push` event and trigger our pipeline to start:

![](../../assets/img/tutorials/tekton-pipelines/webhook_create_3.png)

2) Wait until pipeline will finish to run:

![](../../assets/img/tutorials/tekton-pipelines/webhook_create_4.png)

3) Go to your cli and run next command to see URL to your app :
<br />`kubectl get ksvc -n ACCOUNT`
<br />Change ACCOUNT to your GitHub account name. So for me it is:
<br />`kubectl get ksvc -n tenst`

![](../../assets/img/tutorials/tekton-pipelines/webhook_create_5.png)

4) Get URL from the output and open it in your browser:

![](../../assets/img/tutorials/tekton-pipelines/webhook_create_6.png)

Congratulations! If you see the message then you successfully finished this tutorial.

Here is what happening behind the scenes with everything configured:
  - New commit create `push` event in GitHub
  - GitHub will send details of created event to the webhook link which we created
  - Knative serving the Service for our webhook which will receive event details
  - Knative event will be created and sent to `sink` Service in Knative
  - `sink` Service will generate new PipelineRun for the Pipeline which we created
  - PipelineRun will trigger two TaskRuns - `buildah` (for build) and `knctl-deploy` (for deployment)
  - `buildah` TaskRun will clone GitHub repo, build new image using Dockerfile and push it to the Docker registry
  - `knctl-deploy` TaskRun will start after `buildah` successfully finishes - it will create new Knative Service and its URL will be like `http://repo_name.account.domain.com`

Let's re-cap what we've done:
  - Created new Kubernetes cluster.
  - Deployed Tekton Pipelines.
  - Deployed Tekton Dashboard.
  - Deployed Tekton Webhooks extention.
  - Deployed ligth version of Istio.
  - Deployed Knative.
  - Created tasks for pipelines.
  - Created pipeline.
  - Created secrets for GitHub and Docker repos.
  - Created new Tekton webhook.
  - Reviewed that the app is successfully builded and deployed after new commit was pushed to GitHub repo.