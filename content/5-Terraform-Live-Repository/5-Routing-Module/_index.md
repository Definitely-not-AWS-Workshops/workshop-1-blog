---
title : "Routing Module"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

The routing module that you are going to implement has the following folder structure:

```git
routing/
│
├── main.tf
│   
├── outputs.tf
│   
├── providers.tf
│   
├── state.tf
│   
├── terraform.tfvars
│   
├── variables.tf
│   
└── versions.tf
```

You then start building the configuration files required for the routing module and triggering the first Terraform Cloud run for the module manually.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *routing/versions.tf*:

```hcl
terraform {
  required_version = ">= 1.0.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.5"
    }
  }
}
```

Fill the following lines of code to *routing/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-routing"
    }
  }
}
```

Fill the following lines of code to *routing/variables.tf*:

```hcl
variable "region" {
  description = "The AWS region of the project"
  type        = string
}

variable "environment" {
  description = "The environment to which the project delploys"
  type        = string
  default     = "dev"
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "hosted_zone_name" {
  description = "The name of the hosted zone"
  type        = string
}
```

Fill the following lines of code to *routing/terraform.tfvars*. Replace *\<your-domain-name\>* to yours, mine is **tulna07.site** for example. You can buy one with Route53 or other domain main registrars.

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

hosted_zone_name = <your-domain-name>
```

Fill the following lines of code to *routing/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *routing/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "routing" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/routing?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  hosted_zone_name = var.hosted_zone_name
}
```

Fill the following lines of code to *routing/outputs.tf*:

```hcl
output "hosted_zone_name_servers" {
  description = "The name servers for the hosted zone"
  value       = module.routing.hosted_zone_name_servers
}

output "hosted_zone_id" {
  description = "The name servers for the hosted zone"
  value       = module.routing.hosted_zone_id
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add routing module" && \
git push
```

**3.** Navigate to your **dev-routing** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/5/5/0001.svg?featherlight=false&width=100pc)

**4.** Scroll down to **Remote state sharing** section. Select **Share with specific workspaces**. Click the **Select workspaces to share with** dropdown, choose **dev-app** and **dev-web**. Click **Save settings**.

![0002](/images/5/5/0002.svg?featherlight=false&width=100pc)

**5.** Back to **dev-routing** Terraform Cloud workspace interface. Click **New run**.

![0003](/images/5/5/0003.svg?featherlight=false&width=100pc)

**6.** Click **Start**.

![0004](/images/5/5/0004.svg?featherlight=false&width=100pc)

**7.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0005](/images/5/5/0005.svg?featherlight=false&width=100pc)

**8.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0006](/images/5/5/0006.svg?featherlight=false&width=100pc)

**9.** Get the name servers that the routing module generates. 

![0007](/images/5/5/0007.svg?featherlight=false&width=100pc)

**10.** Add your Route53 name servers from step **9** to your registrar where you buy your domain name. Mine is [GoDaddy](https://www.godaddy.com/en-sg/offers/godaddy), for example.

![0008](/images/5/5/0008.svg?featherlight=false&width=100pc)