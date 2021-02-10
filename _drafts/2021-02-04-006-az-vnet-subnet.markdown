---
layout: post
title: Provision Azure Networking Resources  with GitHub Actions
date: 2021-01-29 00:00:00 +0530
description: Creating azure virtual network (vnet) and subnets using github actions.
comments: false
tags: [Azure, GitHub, CI/CD, GitHub Actions, Virtual network, vnet, subnet]
---

## Azure Networking

Networking is one of the core service offered by Azure.  
Any resource in azure that deals with IP address (private or public), has to be created under a **Virtual Network (vnet)**.

Hence Vnet allows us to define address space and we can associate a particular IP range (CIDR) with a vnet.  
Under vnet, we can divide this address space into many parts, to house different kind of workloads (like private, public, web, application, database etc.). Each of this part forms a **Subnet**.

Hence we can have subnets like private-subnet, web-subnet, db-subnet etc.

It is a good idea to create each vnet per environment under azure non-prod subscription.  
Hence you can have dev-vnet, test-vet, staging-vnet, uat-vnet etc.

In our case, we will create a <u>dev-vnet</u> in azure non-prod subscription and later on we will create prod-vnet in azure production subscription.

The vnet will be provisioned with following properties

- **Resource group:** ainc-pmod-eus-dev-001-rg
- **Location:** East US
- **Vnet name:** ainc-pmod-eus-dev-vnet
- **Address space:** 10.1.0.0/18
- **Subnet name:** aks-subnet
- **Subnet ip range:** 10.1.0.0/18

<u>Note:</u> To understand resource naming convention further, please [visit this link](/004-provision-az-resources/)

The address space ends with "/18".  
So this vnet will have a capability to provide **16,384** IP addresses and will have ip address range of 10.1.0.0 - 10.1.63.255  
It will contain 1 subnet (aks-subnet) which will use all the above IP addresses.

### Pipeline

Lets create a pipeline file that can be used to create these vnet and subnet.  
Create a file named **30-dev-vnet.yml** in <u>repoPlatform</u>, as shown below.

```yaml
name: Create VNet and subnets
on:
  push:
    branches:
      - main
    paths:
      - ".github/workflows/30-dev-vnet.yml"

env:
  DEV_RES_GRP: ainc-pmod-eus-dev-001-rg
  LOCATION: eastus
  VNET_NAME: ainc-pmod-eus-dev-vnet
  VNET_ADDR_SPACE: 10.1.0.0/18
  SUBNET_NAME: aks-subnet
  SUBNET_IP_RANGE: 10.1.0.0/18

jobs:
  create-vnet-subnet:
    runs-on: ubuntu-latest
    steps:
      # Azure Login
      - name: Azure Login
        uses: azure/login@v1
        with:
          creds: "${{ secrets.AZ_DEV_SUB_CRED }}"

      # Creating VNet
      - name: Creating VNet
        uses: azure/CLI@v1
        with:
          azcliversion: latest
          inlineScript: |
            az network vnet create \
              --resource-group ${{ env.DEV_RES_GRP }} \
              --name ${{ env.VNET_NAME }} \
              --address-prefixes ${{ env.VNET_ADDR_SPACE }} \
              --subnet-name ${{ env.SUBNET_NAME }} \
              --subnet-prefix ${{ env.SUBNET_IP_RANGE }} \
              --location ${{ env.LOCATION }} \
              --tags scope=project project=pmod env=dev
```

On commit of this file, GitHub actions will queue this pipeline for execution, as shown below

| ![](/assets/img/006/006-az-vnet-pipeline-1.png) |

The pipline contains a single job (create-vnet-subnet)

| ![](/assets/img/006/006-az-vnet-pipeline-2.png) |

Also, as seen below, we got confirmation that vnet was created successfully in Azure

| ![](/assets/img/006/006-az-vnet-pipeline-3.png) |

And we can see the pipeline took 1min2s of time to complete execution

| ![](/assets/img/006/006-az-vnet-pipeline-4.png) |

And we can check the result in azure portal

| ![](/assets/img/006/006-az-vnet-created-5.png) |

## Conclusion

This pipeline can be used to make any changes to this vnet in future.
