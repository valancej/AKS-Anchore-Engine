# Installing Anchore Engine on an Azure Kubernetes Service cluster with Helm

## Introduction

In this post I will walkthrough deploying an AKS Cluster using the Azure CLI. Once the cluster has been deployed, [Anchore Engine](https://github.com/anchore/anchore-engine) will be installed and run via [Helm](https://helm.sh) on the cluster. Following the install, I will configure Anchore to authenticate with Azure Container Registry (ACR) and analzye an image.

## Prerequisites

- [Azure Subscription](https://azure/com)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm CLI](https://docs.helm.sh/using_helm/#installing-helm)

## Create Azure resource group and AKS cluster

In order to create a cluster, a resource group must first be created in Azure. 

Azure CLI: 

`az group create --name anchoreAKSCluster --location eastus`

Once the resource group has been created, we can create a cluster. The following command creates a cluster name *anchoreAKSCluster* with three nodes.

Azure CLI:

`az aks create --resource-group anchoreAKSCluster --name anchoreAKSCluster --node-count 3 --enable-addons monitoring --generate-ssh-keys`

Once the cluster has been created, use [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) to manage the cluster. To install it locally use the following command: 

Azure CLI:

`az aks install-cli`

Configure kubectl to connect to the cluster you just created:

Azure CLI:

`az aks get-credentials --resource-group anchoreAKSCluster --name anchoreAKSCluster`

In order to verify a successfull connection run the following:

`kubectl get nodes`

## Helm configuration

Prior to deploying Helm in an RBAC-enabled cluster, you must create a service account and role binding for the Tiller service. 

Create a file name `helm-rbac.yaml`: 

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: kube-dashboard
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: rook-operator
  namespace: rook-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: kube-dashboard
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```
