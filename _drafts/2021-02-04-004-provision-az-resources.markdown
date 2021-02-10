---
layout: post
title: Create Infrastructure pipeline with GitHub Actions
date: 2021-01-29 00:00:00 +0530
description: Provision azure resources using github actions.
comments: false
tags: [Azure, GitHub, CI/CD, GitHub Actions, Crossplane, AzureServiceOperator]
---

## Automated Infrastructure pipelines

It is a common expectation now a days that all infrastructure be provisioned using automation.  
This is popularly known in industry as **Infrastructure-as-code (IAC)**.

So we will be building pipelines in GitHub, that will get triggred automatically and will be responsible for creating required resources in azure cloud.  
The scripts for creating these resources will be packaged and executed in pipelines by **GitHub Actions**.

Our trigger for automatic execution of this pipeline is changes (commit) to the pipeline yaml file.  
So essentially, we will be creating infrastructure by making commit to Git. That is considered the best way to provision resources, where Git becomes the single source of truth for changes happening to instructure hosted on cloud.

It also provides better tracability on who made what change when and you can plug in reviewers in the process who can approve and prevent inadvertent things happening to your infrastructure hosted on cloud.

This will be <u>the only way</u> for any team member to create resources in azure cloud. And this processs will be governed by pull-request(PR).  
From my experience, giving people adhoc and direct way to run scripts or access to azure portal, to create resources, leads to a lot of waste and no control over what is going on in azure subscrption.  
So it is important to establish tight controls in managing cloud resources, as the costs add up very quickly and exponentially, and within a year's time, you will start seeing your cloud bill swell to large amounts.

But before we start creating pipelines, let us define some things for the project clearly.

## Subscriptions

We will be using the following azure subscriptions

- **Azure NonProd**: This subscription can contain all <u>non-production environments</u> (like dev, test, staging, qa, uat etc. )
- **Azure Prod**: This subscription will contain all resources belonging to <u>production</u> environment.

## Environments

For the same of simplicity, we will be using only two environments in this project

- Development (dev)
- Production (prd)

## Resource Naming Convention

We will follow the below naming convention for resource created in azure public cloud

**Company**-**Project**-**Location**-**Environment**-**ResourceName**-**ResourceTypeAbbrevation**

**Company:** Ashland Inc. (ainc)  
**Project:** Platform Modernization (pmod)  
**Location:** East US (eus)  
**Environment:** Development (dev), Production(prd)  
**ResourceName:** Unique name of resource (optional)  
**ResourceTypeAbbrevation:** "rg" for resource group, "vnet" for virtual network etc.

## Tags

Every resource created will have appropriate tags to identify where it belongs.
Some of tags that will be used are:

- scope = subscription, project
- project = pmod
- environment(env) = dev, prd

## Resource group

Azure provides **resource groups** as a means of grouping related resources.  
In our case, we will need 2 resource groups

- one for holding resources common to a subscription
- one for holding resources for a particular environmnet.

Hence we will create the following resource groups.

- ainc-pmod-eus-common-rg
- ainc-pmod-eus-dev-001-rg

## Building pipelines

With the project planning out of the way, now we will focus our attention on pipeline.

### Service Principal

For our pipelines to work, we first need to create a service principal with **owner** role, as this role allows us to lock resources created in azure. The scope of this service principal will be at **subscription** level, so that it has permissions to do anything in our azure non-prod subscription

```
az ad sp create-for-rbac ^
  --name "sp-az-gh-actions" ^
  --sdk-auth ^
  --role owner ^
  --scopes /subscriptions/ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1
```

| ![](/assets/img/004/2-common-sp-create.png) |

As you can see, we have successfully created a service principal in Azure. We can check the same in portal as shown below

| ![](/assets/img/004/2-common-sp-create-ok.png) |

We can also verify it has owner permission to azure subscription, as shown below

| ![](/assets/img/004/2-common-sp-verify-sub-permission.png) |

### GitHub Configuration

Next, we need to configure above service principal details in GitHub, so it can be used by piplines.

All our azure infrastructure related scripts and code will be stored in <u>repoPlatform</u>.  
So open this repo, go to its settings and click <u>Secrets</u> option on left side as shown below

| ![](/assets/img/004/3-az-credentials-secret-storing.png) |

Click on New repository secret button and add our service principal output as shown below.

We are naming this secret as AZ_DEV_SUB_CRED, which signifies it is credential for azure dev subscription

| ![](/assets/img/004/3-az-credentials-secret-storing-add-secret-OK.png) |

This completes the configuration part. Now lets focus on building the actual pipeline

### Yaml file for pipeline

Last step is to create yaml files (.yml), that represent contents of the pipelines.  
GitHub expects this files to be placed in path ".github/workflows".

So let us create a file named **10-dev-resource-group.yml** in <u>repoPlatform</u>, as shown below.

| ![](/assets/img/004/4-pipeline-yml-file.png) |

The contents of the file will be as follows.

```yaml
name: Create Resource Groups
on:
  push:
    branches:
      - main
    paths:
      - ".github/workflows/10-dev-resource-group.yml"

env:
  COMMON_RES_GRP: ainc-pmod-eus-common-rg # for common subscription resources
  DEV_RES_GRP: ainc-pmod-eus-dev-001-rg # for Dev environment resources
  LOCATION: eastus

jobs:
  create-resource-groups:
    runs-on: ubuntu-latest
    steps:
      # 1. Azure Login
      - name: Azure Login
        uses: azure/login@v1
        with:
          creds: "${{ secrets.AZ_DEV_SUB_CRED }}"

      # 2. Creating common resource group and locking it
      - name: Creating common resource group and locking it
        uses: azure/CLI@v1
        with:
          azcliversion: latest
          inlineScript: |
            az group create \
              --name ${{ env.COMMON_RES_GRP }} \
              --location ${{ env.LOCATION }} \
              --tags scope=subscription project=pmod env=common

            az lock create \
              --name lck-${{ env.COMMON_RES_GRP }} \
              --lock-type CanNotDelete \
              --resource-group ${{ env.COMMON_RES_GRP }}

      # 3. Creating dev resource group and locking it
      - name: Creating dev resource group and locking it
        uses: azure/CLI@v1
        with:
          azcliversion: latest
          inlineScript: |
            az group create \
              --name ${{ env.DEV_RES_GRP }} \
              --location ${{ env.LOCATION }} \
              --tags scope=project project=pmod env=dev

            az lock create \
              --name lck-${{ env.DEV_RES_GRP }} \
              --lock-type CanNotDelete \
              --resource-group ${{ env.DEV_RES_GRP }}
```

As soon as you commit this file to GitHub, you will notice this workflow appear under the **Actions** tab of GitHub portal. If you click it, you can see it is <u>Queued</u> and waitng for execution.  
Also you can see that this workflow only contains single job called "create-resource-groups"

| ![](/assets/img/004/5-gh-actions-az-pipeline-queued-1.png) |

If you click on the job, you can see its execution in realtime.

| ![](/assets/img/004/5-gh-actions-az-pipeline-executing-2.png) |

When the job is finished, you can it as <u>completed</u>, with time taken for each step.

| ![](/assets/img/004/5-gh-actions-az-pipeline-executing-jobs-completed-3.png) |

If you expand any "creating..." step, you can see output returned from azure after successful provision of that resource.

| ![](/assets/img/004/5-gh-actions-az-pipeline-executing-jobs-output-4.png) |

Finally if you come back to Actions tab, you will see the successfull execution of pipeline (green circle icon) and the total duration of execution of pipeline (1m11s).

| ![](/assets/img/004/5-gh-actions-az-pipeline-executed-5.png) |

Hooray!!!. You have successfully created your pipeline using GitHub Actions and executed it successfully. :blush:

### Checking on azure portal

Now lets verify whether we can see these resources in azure portal. As you can see below, our resource groups were created exactly as scripted in pipeline.

| ![](/assets/img/004/6- resource-groups-created-in-azure.png) |

We can open a resource and vefity it is tagged appropriately

| ![](/assets/img/004/6- resource-groups-created-in-azure-tags-2.png) |

Also, we can verify it is <u>locked</u> for <u>deletion</u>, as we desired.

| ![](/assets/img/004/6- resource-groups-created-in-azure-locked-3.png) |

## Conclusion

So now we have established a process, where we can create various yaml files and commit it to git and it will automatically create/change/delete resources in azure.

**However, Is this the best way to provision resources?**

IMHO, the best way to provision resources is if the resource creation scripts can be directly written in yaml format.

In our case, we are embedding Azure CLI scripts in yaml file, in order to create resources.

AWS was the first cloud to give this option using what they call <mark>CloudFormation Templates</mark>.  
I had [written a blog post on it in 2019](https://adventuresincloud.wordpress.com/2019/05/18/8-projectx-automating-deployment-using-aws-cloudformation-2/), when i was working on AWS cloud.

However a lot has changed after that.

## The Future of IAC - IAC 2.0

On Azure cloud, you have essentially 3 ways of provisioning resources.

1. Using Azure CLI scripts
2. Using ARM templates
3. Using Powershell scripts.

Out of the above, the first two are idempotent and so they are the preffered choice for building infrastructure pipelines.

However, ARM templates tend to become very large very soon. What you can represent in 100 lines in ARM templates can be concisely written in 20 or less lines using azure cli scripts.  
So my preffered choice of creating infrastructure right now is <u>Azure CLI</u>.

But we all know that writing yaml files is a lot better than struggling with scripts, templates, json fiels etc.

So my ideal choice would be if someone provides an option to create azure resources using yaml templates.  
It turns out, the following projects do exactly the same thing:

- [Crossplane](https://crossplane.io/)
- [Azure Service Operator (ASO)](https://github.com/Azure/azure-service-operator)

Both projects follow similar philosophy of hosting their software modules on a kubernetes cluster.  
After that initial setup is done, you can define your resources in yaml file and when that file is applied to cluster, the resources will be created in public cloud.

**ASO** is specific to Azure cloud but **Crossplane** is cloud agnostic.  
This means, using the same crossplane setup anywhere, you can provision resources on many public clouds like Azure, AWS, GCP and more.

But both these products are still in development and do not support creation of all resources yet.  
So we will have to wait for a while before they get more mature and are ready for use in live projects.
