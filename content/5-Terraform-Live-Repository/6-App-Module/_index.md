---
title : "App Module"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 5.6 </b> "
---

The app module that you are going to implement has the following folder structure:

```git
app/
│
├── app-definition.tpl
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

You then start building the configuration files required for the app module and triggering the first Terraform Cloud run for the module manually.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *app/app-definition.tpl*:

```json
{
    "name": "container-1",
    "image": "${image_url}",
    "cpu": 0,
    "portMappings": [
        {
            "containerPort": ${app_port},
            "protocol": "tcp",
            "appProtocol": "http"
        }
    ],
    "essential": true,
    "environment": [],
    "environmentFiles": [],
    "mountPoints": [],
    "volumesFrom": [],
    "ulimits": [],
    "systemControls": []
}
```

Fill the following lines of code to *app/dependencies.tf*:

```hcl
data "terraform_remote_state" "network" {
  backend = "remote"

  config = {
    organization = "aws-first-cloud-journey"
    workspaces = {
      name = "dev-network"
    }
  }
}

data "terraform_remote_state" "image_repository" {
  backend = "remote"

  config = {
    organization = "aws-first-cloud-journey"
    workspaces = {
      name = "dev-image-repository"
    }
  }
}

data "terraform_remote_state" "database" {
  backend = "remote"

  config = {
    organization = "aws-first-cloud-journey"
    workspaces = {
      name = "dev-database"
    }
  }
}

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

data "aws_subnets" "public" {
  filter {
    name   = "vpc-id"
    values = [data.terraform_remote_state.network.outputs.vpc_id]
  }

  tags = {
    Tier = "public"
  }
}

data "aws_subnets" "app" {
  filter {
    name   = "vpc-id"
    values = [data.terraform_remote_state.network.outputs.vpc_id]
  }

  tags = {
    Tier = "app"
  }
}
```

Fill the following lines of code to *app/versions.tf*:

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

Fill the following lines of code to *app/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      project = "workshop-1"
      name    = "dev-app"
    }
  }
}
```

Fill the following lines of code to *app/variables.tf*:

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
  description = "The domain name of the application"
  type        = string
}

variable "app_name" {
  description = "The name of the application"
  type        = string
}

variable "app_port" {
  description = "The port of the application"
  type        = number
}

variable "github_org" {
  description = "The GitHub organization that the GitHub Actions role trusts"
  type        = string
}

variable "github_repo" {
  description = "The GitHub repository that the GitHub Actions role trusts"
  type        = string
}

variable "cpu" {
  description = "Number of cpu units used by the task"
  type        = string
}

variable "memory" {
  description = "Amount (in MiB) of memory used by the task"
  type        = string
}

variable "desired_count" {
  description = "Number of instances of the task definition"
  type        = number
}

variable "max_capacity" {
  description = "The maximum number of tasks allowed in service"
  type        = number
  default     = 4
}

variable "min_capacity" {
  description = "The minimum number of tasks allowed in service"
  type        = number
  default     = 1
}
```

Fill the following lines of code to *app/terraform.tfvars*. Replace *\<your-custom-alb-domain-name\>* to yours, mine is **app.tulna07.site**. Replace *\<your-github-username\>* to your GitHub username, mine is **tulna07**, for example.

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

domain_name = <your-custom-alb-domain-name>
app_name    = "app"
app_port = 8080

github_org  = <your-github-username>
github_repo = "workshop-1-web-app"

cpu           = 1024
memory        = 2048
desired_count = 3
max_capacity  = 5
min_capacity  = 2
```

Fill the following lines of code to *app/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *app/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "app" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/app?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  vpc_id               = data.terraform_remote_state.network.outputs.vpc_id
  hosted_zone_id       = data.terraform_remote_state.routing.outputs.hosted_zone_id
  gh_oidc_provider_arn = data.terraform_remote_state.security.outputs.gh_oidc_provider_arn
  ecr_repository_arn   = data.terraform_remote_state.image_repository.outputs.repository_arn
  dynamodb_table_arn   = data.terraform_remote_state.database.outputs.dynamodb_table_arn

  alb_subnet_ids = data.aws_subnets.public.ids
  app_subnet_ids = data.aws_subnets.app.ids

  domain_name = var.domain_name
  app_name    = var.app_name

  github_org  = var.github_org
  github_repo = var.github_repo

  cpu           = var.cpu
  memory        = var.memory
  desired_count = var.desired_count
  max_capacity  = var.max_capacity
  min_capacity  = var.min_capacity

  app_port = var.app_port

  container_definitions = format("[%s]",
    join(",",
      [templatefile("app-definition.tpl", {
        image_url = "${data.terraform_remote_state.image_repository.outputs.repository_url}:v0.0.0"
        app_port  = var.app_port
        }
      )]
    )
  )
}
```

Fill the following lines of code to *app/outputs.tf*:

```hcl
output "gha_role_deploy_app_arn" {
  description = "The role arn for GitHub Actions that deploys app"
  value       = module.app.gha_role_deploy_app_arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add app module" && \
git push
```

**3.** Navigate to **dev-app** Terraform Cloud workspace interface. Click **New run**.

![0001](/images/5/6/0001.svg?featherlight=false&width=100pc)

**4.** Click **Start**.

![0002](/images/5/6/0002.svg?featherlight=false&width=100pc)

**5.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0003](/images/5/6/0003.svg?featherlight=false&width=100pc)

**6.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0004](/images/5/6/0004.svg?featherlight=false&width=100pc)

**7.** Check out your AWS ECS Service running app tier, you see 3 out of 3 running tasks as the desired count set to 3 in *app/terraform.tfvars* file above.

![0005](/images/5/6/0005.svg?featherlight=false&width=100pc)

**8.** Check out your app tier with the Application Load Balancer custom domain name you has named in *app/terraform.tfvars* file above. Mine is **app.tulna07.site**, for example. It shows the private IP of one of the running tasks managed by the AWS ECS Service in the cluster.

![0006](/images/5/6/0006.svg?featherlight=false&width=100pc)

![0007](/images/5/6/0007.svg?featherlight=false&width=100pc)

**9.** Note down the output of this module after applying, which is **gha_role_deploy_app_arn**. You are going to need the arn to set up your GitHub Actions. 

![0008](/images/5/6/0008.svg?featherlight=false&width=100pc)
