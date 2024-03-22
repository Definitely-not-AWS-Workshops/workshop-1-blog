---
title : "Web Module"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 5.7 </b> "
---

The web module that you are going to implement has the following folder structure:

```git
web/
│
├── dependencies.tf
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

You then start building the configuration files required for the web module and triggering the first Terraform Cloud run for the module manually.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *web/dependencies.tf*:

```hcl
data "terraform_remote_state" "security" {
  backend = "remote"

  config = {
    organization = "aws-first-cloud-journey"
    workspaces = {
      name = "dev-security"
    }
  }
}

data "terraform_remote_state" "routing" {
  backend = "remote"

  config = {
    organization = "aws-first-cloud-journey"
    workspaces = {
      name = "dev-routing"
    }
  }
}
```

Fill the following lines of code to *web/versions.tf*:

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

Fill the following lines of code to *web/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-web"
    }
  }
}
```

Fill the following lines of code to *web/variables.tf*:

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

variable "domain_name" {
  description = "The domain name of the website"
  type        = string
}

variable "alb_domain_name" {
  description = "The domain name of the alb"
  type        = string
}

variable "github_org" {
  description = "The GitHub organization that the GitHub Actions role trusts"
  type        = string
}

variable "github_repo" {
  description = "The GitHub repository that the GitHub Actions role trusts"
  type        = string
}
```

Fill the following lines of code to *web/terraform.tfvars*. Replace *\<your-animehub-domain-name\>* to yours, mine is **animehub.tulna07.site**, for example. Replace *\<your-custom-alb-domain-name\>* to yours, mine is **app.tulna07.site** set in the previous section [5.6 App Module](../6-app-module/) in *app/terraform.tfvars* file. Replace *\<your-github-username\>* to your GitHub username, mine is **tulna07**, for example.

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

domain_name     = <your-animehub-domain-name>
alb_domain_name = <your-custom-alb-domain-name>
github_org           = <your-github-username>
github_repo          = "workshop-1-web-app"
```

Fill the following lines of code to *web/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *web/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "web" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/web?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  hosted_zone_id       = data.terraform_remote_state.routing.outputs.hosted_zone_id
  gh_oidc_provider_arn = data.terraform_remote_state.security.outputs.gh_oidc_provider_arn

  domain_name     = var.domain_name
  alb_domain_name = var.alb_domain_name

  github_org  = var.github_org
  github_repo = var.github_repo

  # Enable for production
  #   enable_bucket_versioning = true
}
```

Fill the following lines of code to *web/outputs.tf*:

```hcl
output "gha_role_deploy_web_arn" {
  description = "The role arn for GitHub Actions that deploys web"
  value       = module.web.gha_role_deploy_web_arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add web module" && \
git push
```

**3.** Navigate to **dev-web** Terraform Cloud workspace interface. Click **New run**.

![0001](/images/5/7/0001.svg?featherlight=false&width=100pc)

**4.** Click **Start**.

![0002](/images/5/7/0002.svg?featherlight=false&width=100pc)

**5.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0003](/images/5/7/0003.svg?featherlight=false&width=100pc)

**6.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0004](/images/5/7/0004.svg?featherlight=false&width=100pc)

**7.** Note down the output of this module after applying, which is **gha_role_deploy_web_arn**. You are going to need the arn to set up your GitHub Actions. 

![0008](/images/5/7/0005.svg?featherlight=false&width=100pc)
