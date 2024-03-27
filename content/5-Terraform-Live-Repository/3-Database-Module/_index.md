---
title : "Database Module"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 5.3 </b> "
---

The database module that you are going to implement has the following folder structure:

```git
database/
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

You then start building the configuration files required for the database module and triggering the first Terraform Cloud run for the module manually.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *database/versions.tf*:

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

Fill the following lines of code to *database/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-database"
    }
  }
}
```

Fill the following lines of code to *database/variables.tf*:

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

variable "table_name" {
  description = "The name of the DynamoDB table"
  type        = string
}

variable "read_capacity" {
  description = "Number of read units for the table"
  type        = number
  default     = 1
}

variable "write_capacity" {
  description = "Number of write units for the table"
  type        = number
  default     = 1
}
```

Fill the following lines of code to *database/terraform.tfvars*:

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

table_name = "Anime"
read_capacity = 2
write_capacity = 2
```

Fill the following lines of code to *database/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *database/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "dynamodb" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/database/dynamodb?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  table_name     = var.table_name
  read_capacity  = var.read_capacity
  write_capacity = var.write_capacity

  # Enable in production
  # enable_deletion_protection = true
}
```

Fill the following lines of code to *database/outputs.tf*:

```hcl
output "dynamodb_table_arn" {
  description = "The arn of the dynamodb table"
  value       = module.dynamodb.table_arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add database module" && \
git push
```

**3.** Navigate to your **dev-database** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/5/3/0001.svg?featherlight=false&width=100pc)

**4.** Scroll down to **Remote state sharing** section. Select **Share with specific workspaces**. Click the **Select workspaces to share with** dropdown and choose **dev-app**. Click **Save settings**.

![0002](/images/5/3/0002.svg?featherlight=false&width=100pc)

**5.** Back to **dev-database** Terraform Cloud workspace interface. Click **New run**.

![0003](/images/5/3/0003.svg?featherlight=false&width=100pc)

**6.** Click **Start**.

![0004](/images/5/3/0004.svg?featherlight=false&width=100pc)

**7.** Wait until the plan is finished. After that, review the plan. 

![0005](/images/5/3/0005.svg?featherlight=false&width=100pc)

**8.** If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0006](/images/5/3/0006.svg?featherlight=false&width=100pc)

**9.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. 

![0007](/images/5/3/0007.svg?featherlight=false&width=100pc)

**10.** After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources.

![0008](/images/5/3/0008.svg?featherlight=false&width=100pc)

**11.** Go to [AWS DynamoDB console](https://console.aws.amazon.com/dynamodb/).

**12.** In the left sidebar, click **Tables** to check out your newly created table.

![0009](/images/5/3/0009.svg?featherlight=false&width=100pc)