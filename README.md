# Installing Anchore Engine on Azure Kubernetes Service cluster with Helm

## Introduction

In this post I will walkthrough deploying an AKS Cluster using the Azure CLI. Once the cluster has been deployed, [Anchore Engine](https://github.com/anchore/anchore-engine) will be installed and run via [Helm](https://helm.sh) on the cluster. Following the install, I will configure Anchore to authenticate with Azure Container Registry (ACR) and analzye an image.

## Prerequisites

- [Azure Subscription](https://azure/com)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

## Create AKS cluster

In order to create a cluster, a resource group must be created in Azure: 

Azure CLI: 
`az group create --name anchoreAKSCluster --location eastus`

