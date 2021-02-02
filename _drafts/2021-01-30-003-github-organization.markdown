---
layout: post
title: Using GitHub Organization to manage Teams and Project
date: 2021-01-29 00:00:00 +0530
description: Manage your teams and project artifacts on github
comments: false
tags: [Git, GitHub]
---

## GitHub Organization

We will use **GitHub Organization** to manage all artifacts for our [Platform Modernization](/002-devops-gh-k8s/) project.

## Teams

Any project requires a minimum of 2 teams to boot. A <u>Business</u> & a <u>Technical</u> team.  
We can start our project with the following teams

- **Business teams**

  1. **Orders** team - responsible for order management functions.
  2. **Shipping** team - responsible for shipping orders.

- **Technical teams**
  1. **Platform team** - responsible for managing azure public cloud infrastructure
  2. **DevOps team** - responsible for all devops activity related to this project.

# Git Repos

Corresponding to the above teams, the following git repos have been created.

- repoOrders
- repoShipping
- repoPlatform
- repoDevOps

|              Team Repos               |
| :-----------------------------------: |
| ![](/assets/img/003/2-four-repos.png) |
