---
title : "Security Module"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 5.4 </b> "
---

The security module that you are going to implement has the following folder structure:

```git
security/
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

You then start building the configuration files required for the security module and triggering the first Terraform Cloud run for the module manually.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *security/versions.tf*:

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

Fill the following lines of code to *security/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-security"
    }
  }
}
```

Fill the following lines of code to *security/variables.tf*:

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
```

Fill the following lines of code to *security/terraform.tfvars*:

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"
```

Fill the following lines of code to *security/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *security/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "security" {
  source = "git::https://github.com/tulna07/workshop-1-tf-modules.git//modules/security?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name
}
```

Fill the following lines of code to *security/outputs.tf*:

```hcl
output "gh_oidc_provider_arn" {
  description = "The arn of GitHub openid connect provider"
  value       = module.security.gh_oidc_provider_arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add security module" && \
git push
```

**3.** Navigate to your **dev-security** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/5/4/0001.svg?featherlight=false&width=100pc)

**4.** Scroll down to **Remote state sharing** section. Select **Share with specific workspaces**. Click the **Select workspaces to share with** dropdown, choose **dev-app** and **dev-web**. Click **Save settings**.

![0002](/images/5/4/0002.svg?featherlight=false&width=100pc)

**5.** Back to **dev-security** Terraform Cloud workspace interface. Click **New run**.

![0003](/images/5/4/0003.svg?featherlight=false&width=100pc)

**6.** Click **Start**.

![0004](/images/5/4/0004.svg?featherlight=false&width=100pc)

**7.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0005](/images/5/4/0005.svg?featherlight=false&width=100pc)

**8.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0006](/images/5/4/0006.svg?featherlight=false&width=100pc)