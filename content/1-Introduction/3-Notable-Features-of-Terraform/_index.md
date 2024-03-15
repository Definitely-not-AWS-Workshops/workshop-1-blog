---
title : "Notable Features of Terraform"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 1.3 </b> "
---

#### Terraform Modules

In Terraform, [modules](https://developer.hashicorp.com/terraform/language/modules) are containers for multiple resources that are used together. A module consists of a collection of *.tf* and/or *.tf.json* files kept together in a directory.

#### Terraform Cloud

Rather than requiring you to manage Terraform runs locally, [Terraform Cloud](https://developer.hashicorp.com/terraform/cloud-docs) manages them in a reliable and consistent environment. The infrastructure can be developed in a manner related to application development by connecting to version control systems and securely storing state and secret data. The Terraform Cloud UI provides a detailed view into the resources managed by a Terraform project and gives enhanced visibility into each Terraform operation.

Your team can work collaboratively on every stage of the infrastructure development process with Terraform Cloud. For instance, your team can evaluate and approve the plan before you implement it every time you plan a new modification. In order to avoid concurrent modifications that can corrupt the state file, it also automatically locks the state during operations.

![Terraform Cloud](/images/1/3/0001.svg?featherlight=false&width=100pc)
