---
layout: post
title: Azure GitHub Actions
date: 2021-01-10 00:00:00 +0530
description: Provision azure infrastructure using github actions
comments: false
tags: [Azure, GitHub]
---

### 1. Create a resource group

```bash
az group create --name learn-gh-actions-rg --location eastus
```

### 2. Create a new AD application

```bash
$appName="myApp"
az ad app create --display-name $appName --homepage 'http://localhost/$appName' --identifier-uris http://localhost/$appName
```

### 3. Create service principal

```bash
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1/resourceGroups/learn-gh-actions-rg --sdk-auth
```

# 4. Create github workflow

Create github workflow file in following path

`.github/worflows/gh-actions.yml`

Put the following contents in this workflow file

```bash
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      # Azure Login
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'

      # Create new resource group
      - name: Azure CLI script to create a new resource group
        uses: azure/CLI@v1
        with:
          azcliversion: latest
          inlineScript: |
            az group create --name testing123-rg --location eastus
            az account list
```

As soon as you check-in the file, you can see workflow execute and successfully login to azure.

#

## Provisiong Resources - The traditional way

Working in a public cloud like Azure is all about using the vast number of services offered by the cloud provider.  
Broadly these service offerings are categorized as follows

- **Infrastructure as a Service (IaaS):** Compute (VM), Storage (Disks), Networking (VNet, Subnet) etc.
- **Platform as a Service(PaaS):** Azure WebApps, Logic Apps, Functions etc.
- **Software as a Service (SaaS):** Azure IoT Suite, Office 365 etc.

All these services can be used by provisioning `Resources` of that type.  
The following options are available for provisioning resources in your azure subscription

1. Azure Portal
2. Azure CLI
3. Azure PowerShell
4. ARM templates

Azure portal requires the least about of learning as it is intutive. However the same is not true for other 3 options.  
In order to use any other options, you have to go through the process of learning how to get things done and learn the idiosyncracies of each method.

## Provisiong Resources - The Kubernetes way

Kubernetes is a container orchestrator that is becoming very popular for hosting various kinds of enterprise workloads (like Applications, Microservices, Machine Learning models etc.).

The way it works is, you define what you need in a `YAML` file and give it to Kubernetes.  
It will make sure to create resources as per specifications defined in your Yaml file. This is known as `Declarative approach` to provisioning resources.

## Provisioning resources - The Modern way

Azure Service Opeartor (ASO) brings the best of both approaches and allows you to provision resources on your azure subscription using **YAML** files.

Once you get used to the ease of defining your resources in YAML format, there is no going back and you will live happily ever after :-)

## Pre-requisites

In order to work with ASO, you will require the following things:

- A Kubernetes cluster running in your Azure subscription
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) command-line utility installed on your computer
- [Helm3](https://helm.sh/docs/intro/install/) installed on your computer.

## Setup

So lets provision a resource group and an AKS cluster.

### Create Resource group

![ResourceGroup](../assets/img/005/1-create-resource-group.png)

### Create AKS cluster

![AKSCluster](../assets/img/005/2-create-aks-cluster.png)

### Install cert-manager on the cluster

`kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v0.12.0/cert-manager.yaml`

### Add the Helm repo for Azure Service Operator

`helm repo add azureserviceoperator https://raw.githubusercontent.com/Azure/azure-service-operator/master/charts `

### Create an Azure Service Principal with Contributor permission so ASO can create resources

AZURE_SUBSCRIPTION_ID = ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1
AZURE_TENANT_ID = 2558b069-6c36-436d-bd5b-72437d64af56

`az ad sp create-for-rbac -n "azure-service-operator" --role contributor --scopes /subscriptions/ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1`

```
{
"appId": "284ee122-d615-4225-8602-8a9540865290",
"displayName": "azure-service-operator",
"name": "http://azure-service-operator",
"password": "df38b161-0572-450e-92c1-f509e60b438b",
"tenant": "2558b069-6c36-436d-bd5b-72437d64af56"
}
```

AZURE_CLIENT_ID = 284ee122-d615-4225-8602-8a9540865290 # appID
AZURE_CLIENT_SECRET = df38b161-0572-450e-92c1-f509e60b438b # password

### Install the Azure Service Operator on your cluster

```
helm upgrade --install aso https://github.com/Azure/azure-service-operator/raw/master/charts/azure-service-operator-0.1.0.tgz \
 --create-namespace \
 --namespace=azureoperator-system \
 --set azureSubscriptionID=$AZURE_SUBSCRIPTION_ID \
        --set azureTenantID=$AZURE_TENANT_ID \
 --set azureClientID=$AZURE_CLIENT_ID \
        --set azureClientSecret=$AZURE_CLIENT_SECRET \
 --set image.repository="mcr.microsoft.com/k8s/azureserviceoperator:latest"
```

```
helm upgrade --install aso https://github.com/Azure/azure-service-operator/raw/master/charts/azure-service-operator-0.1.0.tgz --create-namespace --namespace=azureoperator-system --set azureSubscriptionID=ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1 --set azureTenantID=2558b069-6c36-436d-bd5b-72437d64af56 --set azureClientID=284ee122-d615-4225-8602-8a9540865290 --set azureClientSecret=df38b161-0572-450e-92c1-f509e60b438b --set image.repository="mcr.microsoft.com/k8s/azureserviceoperator:latest"
```

Check ASO installation

`kubectl get pods -n azureoperator-system`

### Create resources in azure.

```
apiVersion: azure.microsoft.com/v1alpha1
kind: ResourceGroup
metadata:
  name: azure-operators-rg
spec:
  location: "westus"
```

`kubectl apply -f ResourceGroup.yaml`

### Delete resources in azure.

`kubectl delete -f ResourceGroup.yaml`

## ASO

ASO is under active development and you can click on this [link](https://github.com/Azure/azure-service-operator#supported-azure-services) to find out which resources are supported currently

Also, you can get more details about it on this [GitHub](https://github.com/Azure/azure-service-operator) page
