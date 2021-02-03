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
| ![](/assets/img/003/2-team-repos.png) |

## Roles

Under each business teams, lets assume there are people fulfilling the following roles:

- Developer
- Technical lead
- Manager

For technical team, we will have a lean hierarchy with only two roles:

- Developer(s)
- Technical Architect

# Project Management

Most of us think of GitHub as provider for hosting git repositories. However GitHub also has many features that allow project management by teams on a regular basis.

### Boards

One of the feature is **Project Boards**. They allow you to visualize the work by adding cards and moving cards across vertical columns as work progresses from different stages like <u>Todo</u>, <u>In Progress</u> & <u>Done</u>.

Since each team will manage their tasks independently, we will proceed to create 1 board for each team

|              Create Project Board               |
| :---------------------------------------------: |
| ![](/assets/img/003/3-create-project-board.png) |

Once you are done, you should be able to see 4 project boards as shown below

|              All Project Boards               |
| :-------------------------------------------: |
| ![](/assets/img/003/4-all-project-boards.png) |

If you open any of one of the boards, you should be able to see columns representing stages representing the journey of how work is planned till its finished. This columns (stages) are configurable as per the project. In each stage, you can add a **card** representing the task that needs to be done.  
When there is progress made, the card can be moved from one column to another and the board depicts the stage of where all works items are at any moment.

|               Project Board Stages               |
| :----------------------------------------------: |
| ![](/assets/img/003/5-project-boards-stages.png) |

### Teams

Another feature is that GitHub supports is creating **teams** and **sub-teams**.  
This helps to organize project teams in a hierarchy and assign different permissions for each team.

In our case, we create a project team with name **Platform Modernization Team**.
Under it, we create 4 teams as per our requirement.

|            Project Teams hierarchy             |
| :--------------------------------------------: |
| ![](/assets/img/003/6-pmod-teams-subteams.png) |

If we click any team, we are taken to that teams landing page, as shown below

|              DevOps Team Landing Page               |
| :-------------------------------------------------: |
| ![](/assets/img/003/7-devops-team-landing-page.png) |

Here we can see our DevOps repository and DevOps project board linked.

|              Linked DevOps Repository              |
| :------------------------------------------------: |
| ![](/assets/img/003/8-devops-team-linked-repo.png) |

While linking the repo, we have given <u>Admin rights</u>, so this team has full control on **repoDevOps**

DevOps board is also linked to this team and as above, we have given this team full control to manage its board

|                   Linked DevOps Boards                   |
| :------------------------------------------------------: |
| ![](/assets/img/003/9-devops-team-linked-proj-board.png) |
