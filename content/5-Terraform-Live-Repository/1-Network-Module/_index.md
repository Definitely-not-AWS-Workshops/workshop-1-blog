---
title : "Network Module"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

The network module that you are going to implement has the following folder structure:

```git
network/
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

You then start building the configuration files required for the network module and triggering the first Terraform Cloud run for the module manually. 

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *network/versions.tf*:

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

Fill the following lines of code to *network/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-network"
    }
  }
}
```
Fill the following lines of code to *network/variables.tf*:

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

variable "azs" {
  description = "The choosen azs to allocate resources in the vpc"
  type        = list(string)
  default     = []
}

variable "vpc_cidr" {
  description = "The cidr block of the vpc"
  type        = string
}

variable "public_subnets" {
  description = "The cidr blocks for the public subnets"
  type        = list(string)
  default     = []
}

variable "private_subnets" {
  description = "The cidr blocks for the private subnets"
  type        = list(string)
  default     = []
}
```

Fill the following lines of code to *network/terraform.tfvars*:

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

vpc_cidr = "10.0.0.0/16"
azs = ["a", "b"]
public_subnets = ["10.0.0.0/18", "10.0.64.0/18"]
private_subnets = ["10.0.128.0/18", "10.0.192.0/18"]
```

Fill the following lines of code to *network/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *network/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "network" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/network?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  vpc_cidr = var.vpc_cidr
  azs      = formatlist("${var.region}%s", var.azs)

  public_subnets = [
    for cidr in var.public_subnets : {
      cidr : cidr
    }
  ]

  private_subnets = [
    for cidr in var.private_subnets : {
      cidr : cidr,
      tier : "app"
    }
  ]
}
```

Fill the following lines of code to *network/outputs.tf*:
```hcl
output "vpc_id" {
  description = "The id of the vpc"
  value       = module.network.vpc_id
}

output "vpc_arn" {
  description = "The arn of the vpc"
  value       = module.network.vpc_arn
}

output "public_subnet_ids" {
  description = "The ids of the public subnets"
  value       = module.network.public_subnet_ids
}

output "private_subnet_ids" {
  description = "The ids of the private subnets"
  value       = module.network.private_subnet_ids
}

output "subnet_ids" {
  description = "The ids of the subnets"
  value       = module.network.subnet_ids
}
```

**2.** Remove the *.gitkeep* file.

```git
rm .gitkeep
```

**3.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "remove .gitkeep and add network module" && \
git push
```

**4.** Navigate to your **dev-network** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/5/1/0001.svg?featherlight=false&width=100pc)

**5.** Scroll down to **Remote state sharing** section. Select **Share with specific workspaces**. Click the **Select workspaces to share with** dropdown and choose **dev-app**. Click **Save settings**.

![0002](/images/5/1/0002.svg?featherlight=false&width=100pc)

**6.** Back to **dev-network** Terraform Cloud workspace interface. Click **New run**.

![0003](/images/5/1/0003.svg?featherlight=false&width=100pc)

**7.** Click **Start**.

![0004](/images/5/1/0004.svg?featherlight=false&width=100pc)

**8.** Wait until the plan is finished. After that, review the plan.

![0005](/images/5/1/0005.svg?featherlight=false&width=100pc)

**9.** If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0006](/images/5/1/0006.svg?featherlight=false&width=100pc)

**10.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. 

![0007](/images/5/1/0007.svg?featherlight=false&width=100pc)

**11.** After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0008](/images/5/1/0008.svg?featherlight=false&width=100pc)

**12.** Go to [AWS VPC console](https://console.aws.amazon.com/vpc/).

**13.** In the left sidebar, click **Your VPCs** to check out your newly created VPC.

![0009](/images/5/1/0009.svg?featherlight=false&width=100pc)

**14.** In the left sidebar, click **Subnets** and then filter with `dev-workshop-1` value to check out your newly created subnets. 

![00010](/images/5/1/00010.svg?featherlight=false&width=100pc)

**15.** In the left sidebar, click **Route tables** to check out your newly created route tables.

![00011](/images/5/1/00011.svg?featherlight=false&width=100pc)

**16.** In the left sidebar, click **Internet gateways** to check out your newly created internet gateway. 

![00012](/images/5/1/00012.svg?featherlight=false&width=100pc)