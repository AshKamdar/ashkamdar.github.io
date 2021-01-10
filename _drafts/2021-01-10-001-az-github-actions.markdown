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
