# GIT OPS Demo


We will use this repo to dockerize the app from 
https://github.com/agentic-harness-engineering/basic-llm-chatbot-local/

Docker -> k8s -> helm -> argocd to demo gitops deployment from version control ( github/gitlab/bitbucket ).
We will use a local docker registry server deployed to the same k8s. In normal CICD piepline, our dockerfile builds the image and pushed to the enterprises docker registry, whereever that may be. 

Our demo environment is a k3s server, https://k3s.io/ deployed to a laptop. 
Anything else can be used. Rancher-desktop for a local k8s environment https://rancherdesktop.io/ or microk8s https://canonical.com/microk8s
or minikube https://minikube.sigs.k8s.io/
or gke / aks / eks 

```bash
kubectl get nodes
NAME        STATUS   ROLES           AGE   VERSION
skilganon   Ready    control-plane   77d   v1.35.4+k3s1
```

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
