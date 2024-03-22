---
title : "Terraform Cloud Workspaces"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

As infrastructure grows, more teams are required to maintain it. As you are aware, infrastructure code can be broken down into smaller modules that not only limit the scope of damage but are also managed by different teams. Terraform Cloud offers the collaborative delivery and management of these modules across teams. Terraform Cloud's main unit is a workspace. A workspace contains everything Terraform needs to run, including a Terraform configuration (often from a VCS repository), values for the configuration's variables, and state data for tracking operations between runs.

In this part, following practices from the Terraform team, you are going to discover Terraform Cloud workspaces and their relationships that are required for AnimeHub.

#### Terraform Cloud Workspace Creation - Recommended Pattern

The Terraform team advises considering the following approach when creating a workspace:

With collaborative Terraform, each module has a specific function and is maintained by specific teams or members with permissions to that module. Workspaces are Terraform Cloud's primary way of delegating control, therefore their structure should correspond to your permissions structure. The most effective approach is to create a single workspace for each environment of a particular infrastructure module.

In other words, 

```git
environments X modules = workspaces
```

You should not use a single Terraform Cloud workspace to manage your whole production or staging environment. Instead, create smaller workspaces that are easily managed. This also means that not all configurations must use the same environments; if a UAT environment is not appropriate for your security infrastructure, you are not required to use one.

For management purposes, name your workspaces with both their environment and their module. As an example, if you have a module for managing an internal billing app and another for your networking infrastructure, you may name the workspaces as follows:

![0001](/images/2/3/0001.svg?featherlight=false&width=35pc)

{{% notice info %}}
Refer to [Terraform Recommended Practices](https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices) for additional recommended practices regarding Terraform Cloud workflows and workspaces from the team.
{{% /notice %}}


You would take the recommendations to identify the Terraform Cloud workspaces needed for AnimeHub.

#### Terraform Cloud Workspace Proposal

Since you determined the modules needed for AnimeHub in the previous section, it is now easier to discover Terraform Cloud workspaces for AnimeHub. For the purpose of demonstration, only workspaces for the development environment will be developed. The following workspaces will be set up in the hands-on sections:

![0002](/images/2/3/0002.svg?featherlight=false&width=50pc)

#### Graph of Dependencies between Terraform Cloud Workspaces

Terraform Cloud workspaces mostly correspond to modules, therefore dependencies between workspaces are likewise dependencies between modules. The graph of dependencies between Terraform Cloud workspaces and their execution order, numbered from **1** to **7**, is illustrated below:

![0003](/images/2/3/0003.svg?featherlight=false&width=45pc)

{{% notice note %}}
It is obvious that several workspaces can be run in parallel; however, you should follow the sequence because Terraform Cloud does not support that. If you attempted to execute the workspace runs concurrently, Terraform Cloud would run the first confirmed run and queue others.
{{% /notice %}}

You are going to implement two Terraform repositories: one for root modules, *workshop-1-tf-live*, and one for reuseable modules, *workshop-1-tf-modules*. The *workshop-1-tf-modules* repository just serves as a *blueprint* for the *workshop-1-tf-live* through environments (dev, staging, prod, etc.). The root modules, therefore, would call reuseable modules. You will explore those in more details in the next hands-on sections.

{{% notice note %}}
In the workshop, both repositories are accessible to the public. In real world, you should consider making your repositories private or moving them to a private Terraform registry.
{{% /notice %}}

Your Terraform Cloud workspaces will be connected to the *workshop-1-tf-live* repository. Updates from the repository that are compatible with your Terraform Cloud workspace settings will trigger a workspace run in Terrafornm Cloud. This may involve a plan for team review before applying or destroying your AWS infrastructure.

You now have the following AWS architecture and deployment pipelines for both AnimeHub platform and its infrastructure:

![0004](/images/2/3/0004.svg?featherlight=false&width=100pc)




