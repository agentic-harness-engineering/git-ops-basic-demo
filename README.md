# GIT OPS Demo


We will use this repo to dockerize the app from 
https://github.com/agentic-harness-engineering/basic-llm-chatbot-local/

Docker -> k8s -> helm -> argocd to demo gitops deployment from version control ( github/gitlab/bitbucket ).
We will use a local docker registry server deployed to the same k8s. In normal CICD piepline, our dockerfile builds the image and pushed to the enterprises docker registry, whereever that may be. 

# Our k8s env
Our demo environment is a k3s server, https://k3s.io/ deployed to a laptop. 
Anything else can be used. Rancher-desktop for a local k8s environment https://rancherdesktop.io/ or microk8s https://canonical.com/microk8s
or minikube https://minikube.sigs.k8s.io/
or gke / aks / eks 

```bash
kubectl get nodes
NAME        STATUS   ROLES           AGE   VERSION
skilganon   Ready    control-plane   77d   v1.35.4+k3s1
```

# HELM
Helm is a Kubernetes Package Manager.  => https://helm.sh/
https://helm.sh/docs/intro/install

brew install helm   ( fyi: if use rancher-desktop or microk8s helm is packaged with the release ) 

We add the argocd chart registry to our local helm

```bash
helm repo add argo https://argoproj.github.io/argo-helm

helm search repo  argo
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
argo/argo                       1.0.0           v2.12.5         A Helm chart for Argo Workflows
argo/argo-cd                    10.2.0          v3.4.5          A Helm chart for Argo CD, a declarative, GitOps...
argo/argo-ci                    1.0.0           v1.0.0-alpha2   A Helm chart for Argo-CI
argo/argo-events                2.4.23          v1.9.11         A Helm chart for Argo Events, the event-driven ...
argo/argo-lite                  0.1.0                           Lighweight workflow engine for Kubernetes
argo/argo-rollouts              2.41.1          v1.9.1          A Helm chart for Argo Rollouts
argo/argo-workflows             1.0.22          v4.0.8          A Helm chart for Argo Workflows
argo/argocd-applicationset      1.12.1          v0.4.1          A Helm chart for installing ArgoCD ApplicationSet
argo/argocd-apps                2.0.5                           A Helm chart for managing additional Argo CD Ap...
argo/argocd-image-updater       1.2.4           v1.2.2          A Helm chart for Argo CD Image Updater, a tool ...
argo/argocd-notifications       1.8.1           v1.2.1          A Helm chart for ArgoCD notifications, an add-o...
bitnami/argo-cd                 11.0.0          3.1.1           Argo CD is a continuous delivery tool for Kuber...
bitnami/argo-workflows          13.0.6          3.7.1           Argo Workflows is meant to orchestrate Kubernet...
```

# ARGO CD
We can the install argocd,

```bash
helm upgrade --install argocd --namespace argocd --create-namespace argo/argo-cd
```
To get the configuration options for argocd 

```bash
helm show values argo/argo-cd
```
piping these to a file, you can then store and deploy from version control if need be

```bash
helm show values argo/argo-cd > argocd-values.yaml
```
you can then edit, save and deploy using custom config:

```bash
helm upgrade --install argocd --namespace argocd --create-namespace argo/argo-cd -f argocd-values.yaml
```



```bash
➜  git-ops-basic-demo git:(main) git submodule add https://github.com/agentic-harness-engineering/basic-llm-chatbot-local/ src
Cloning into 'agentic-harness-engineering/git-ops-basic-demo/src'...
remote: Enumerating objects: 11, done.
remote: Counting objects: 100% (11/11), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 11 (delta 1), reused 8 (delta 1), pack-reused 0 (from 0)
Receiving objects: 100% (11/11), done.
Resolving deltas: 100% (1/1), done.
➜  git-ops-basic-demo git:(main) ✗ ls
README.md src
➜  git-ops-basic-demo git:(main) ✗ ls src
app.py           README.md        requirements.txt
```


After adding Dockerfile to our src build
```bash



➜  src git:(main) docker build -t streamlitllmtest .
[+] Building 1.0s (11/11) FINISHED                                                                                                      docker:rancher-desktop
 => [internal] load build definition from Dockerfile                                                                                                      0.0s
 => => transferring dockerfile: 234B                                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/python:alpine                                                                                          0.9s
 => [internal] load .dockerignore                                                                                                                         0.0s
 => => transferring context: 2B                                                                                                                           0.0s
 => [1/6] FROM docker.io/library/python:alpine@sha256:26730869004e2b9c4b9ad09cab8625e81d256d1ce97e72df5520e806b1709f92                                    0.0s
 => => resolve docker.io/library/python:alpine@sha256:26730869004e2b9c4b9ad09cab8625e81d256d1ce97e72df5520e806b1709f92                                    0.0s
 => [internal] load build context                                                                                                                         0.0s
 => => transferring context: 1.07kB                                                                                                                       0.0s
 => CACHED [2/6] RUN apk upgrade --no-cache                                                                                                               0.0s
 => CACHED [3/6] COPY requirements.txt /tmp                                                                                                               0.0s
 => CACHED [4/6] RUN pip install -r /tmp/requirements.txt                                                                                                 0.0s
 => CACHED [5/6] WORKDIR /src/                                                                                                                            0.0s
 => [6/6] COPY app.py /src/                                                                                                                               0.0s
 => exporting to image                                                                                                                                    0.1s
 => => exporting layers                                                                                                                                   0.0s
 => => exporting manifest sha256:c4c018d5b29175870ba68cf5c4ee49c88aec1b115e39cd0b0000b6e93e0a8e17                                                         0.0s
 => => exporting config sha256:d473dfcfc359b522b48da4986408dd8f48d2af539f57368eb1f090113728e48f                                                           0.0s
 => => exporting attestation manifest sha256:e0e452f7139650ea73abc2ef775c9c6d32726251de8f199fda9506d694dde5c2                                             0.0s
 => => exporting manifest list sha256:31f4902a6c1f80b3abc3da53fc55a0eb9546f47cc5a83aa38b458d0ff5eaa9a6                                                    0.0s
 => => naming to docker.io/library/streamlitllmtest:latest                                                                                                0.0s
 => => unpacking to docker.io/library/streamlitllmtest:latest                                                                                             0.0s
```

TEST running it:
```bash
➜  src git:(main) docker run streamlitllmtest

Collecting usage statistics. To deactivate, set browser.gatherUsageStats to false.

2026-07-23 15:20:03.016 Uvicorn server started on :::8501

  You can now view your Streamlit app in your browser.

  Local URL: http://localhost:8501
```
