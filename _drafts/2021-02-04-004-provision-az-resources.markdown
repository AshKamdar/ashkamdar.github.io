---
layout: post
title: Provision Azure resources using GitHub Actions
date: 2021-01-29 00:00:00 +0530
description: Building infrastructure pipelines with github actions to provision azure resources.
comments: false
tags: [Azure, GitHub, CI/CD]
---

## GitHub Organization

# Subscriptions

As discussed previously, we will be using the following 2 azure subscriptions

- Azure NonProd: This subscription can contain all <u>non-production environments</u> (like dev, test, staging, qa, uat etc. )
- Azure Prod: This subscription will contain all resources belonging to <u>production</u> environment.

# Environments

For the same of simplicity, we will be using only 2 environments in this project

- Development (dev)
- Production (prd)

# Resource Naming Convention

We will follow the below naming convention for resource created in azure public cloud

**Company**-**Project**-**Location**-**Environment**-**ResourceName**-**ResourceTypeAbbrevation**

**Company:** Ashland Inc. (ainc)  
**Project:** Platform Modernization (pmod)  
**Location:** East US (eus)  
**Environment:** Development (dev), Production(prd)  
**ResourceName:** Unique name of resource (optional)  
**ResourceTypeAbbrevation:** e.g. Resource group (rg), Virtual Network (vnet) etc.

# Tags

Every resource created will have appropriate tags to identify where it belongs.
Some of tags that will be used are:

1. Scope = Subscription, Project
2. Project = Pmod
3. Environment(Env) = Dev,Prd

# Resource group

Azure provides **resource groups** as a means of grouping related resources.  
In our case, we will need 2 resource groups

- one for holding resources common to a subscription
- one for holding resources for a particular environmnet.

Hence we will create the following resource groups.

- ainc-pmod-eus-common-rg
- ainc-pmod-eus-dev-001-rg

```
az group create ^
  --name ainc-pmod-eus-common-rg ^
  --location eastus ^
  --tags scope=subscription project=pmod env=common
```

# Service Principal

```
az ad sp create-for-rbac ^
  --name "sp-az-gh-actions" ^
  --sdk-auth ^
  --role owner ^
  --scopes /subscriptions/ca3d9655-27d1-4dcc-a08f-4a4dbc6a6fc1
```
