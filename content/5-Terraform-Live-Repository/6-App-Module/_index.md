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

**5.** Wait until the plan is finished. After that, review the plan. 

![0003](/images/5/6/0003.svg?featherlight=false&width=100pc)

**6.** If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0004](/images/5/6/0004.svg?featherlight=false&width=100pc)

**7.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. 

![0005](/images/5/6/0005.svg?featherlight=false&width=100pc)

**8.** After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources.

![0006](/images/5/6/0006.svg?featherlight=false&width=100pc)

**9.** Go to [AWS IAM console](https://console.aws.amazon.com/iam/).

**10.** In the left sidebar, click **Roles** to check out your newly created roles. Filter with `gha-role-for-deploying-app` and `allow-to-communicate-with-dynamodb` values to see your roles.

![0007](/images/5/6/0007.svg?featherlight=false&width=100pc)

![0008](/images/5/6/0008.svg?featherlight=false&width=100pc)

**11.** Go to [AWS VPC console](https://console.aws.amazon.com/vpc).

**12.** In the left sidebar, click **Endpoints** to check out your newly created endpoints.

![0009](/images/5/6/0009.svg?featherlight=false&width=100pc)

**13.** Go to [AWS ECS console](https://console.aws.amazon.com/ecs).

**14.** In the left sidebar, click **Task definitions** to check out your newly created task definition.

![00010](/images/5/6/00010.svg?featherlight=false&width=100pc)

**15.** In the left sidebar, click **Clusters** to check out your newly created cluster.

![00011](/images/5/6/00011.svg?featherlight=false&width=100pc)

**16.** Go to [AWS ACM console](https://console.aws.amazon.com/acm).

**17.** In the left sidebar, click **List certificates** to check out your newly created certificate.

![00012](/images/5/6/00012.svg?featherlight=false&width=100pc)

**18.** Check out your AWS ECS Service running app tier, you see 3 out of 3 running tasks as the desired count set to 3 in *app/terraform.tfvars* file above. You may see 2 instead after a while since the Auto Scaling Group changed the target count to 2 to keep up with the scaling policy.

![00013](/images/5/6/00013.svg?featherlight=false&width=100pc)

**19.** Check out your app tier with the Application Load Balancer custom domain name you has named in *app/terraform.tfvars* file above. Mine is [app.tulna07.site](https://app.tulna07.site/), for example. It shows the private IP of one of the running tasks managed by the AWS ECS Service in the cluster.

![00014](/images/5/6/00014.svg?featherlight=false&width=100pc)

![00015](/images/5/6/00015.svg?featherlight=false&width=100pc)

**20.** Note down the output of this module after applying, which is **gha_role_deploy_app_arn**. You are going to need the arn to set up your GitHub Actions. 

![00016](/images/5/6/00016.svg?featherlight=false&width=100pc)

**21.** However, your Application Load Balancer should only accept access from CloudFront, according to AWS best practices. In app module of *workshop-1-tf-modules* local repository, add or change the following Terraform configurations:

In *modules/app/dependencies.tf* file, add

```hcl
data "aws_ec2_managed_prefix_list" "cloudfront" {
  filter {
    name   = "prefix-list-name"
    values = ["com.amazonaws.global.cloudfront.origin-facing"]
  }
}
```

In *modules/app/main.tf* file, change

```hcl
resource "aws_vpc_security_group_ingress_rule" "allow_all_to_alb" {
  security_group_id = module.alb.security_group_id
  cidr_ipv4         = local.all_ips
  ip_protocol       = local.any_protocol
}
```

to

```hcl
resource "aws_vpc_security_group_ingress_rule" "allow_cloudfront" {
  security_group_id = module.alb.security_group_id

  prefix_list_id = data.aws_ec2_managed_prefix_list.cloudfront.id
  ip_protocol    = "tcp"
  from_port      = 443
  to_port        = 443
}
```

**22.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "change alb ingress rule to allow cloudfront" && \
git push
```

**23.** Create a git tag with the provided version and message.

```git
git tag -a "v2.0.0" -m "release v2.0.0"
```

**24.** Push the tag to the GitHub repository.

```git
git push --follow-tags
```

**25.** Update your source app module in the *workshop-1-tf-live* local repository to **v2.0.0**.

In *environments/dev/app/main.tf* file, change

```hcl
module "app" {
  source = "git::https://github.com/tulna07/workshop-1-tf-modules.git//modules/app?ref=v1.0.0"
 
  # Other inputs
  # ...
}
```

to

```hcl
module "app" {
  source = "git::https://github.com/tulna07/workshop-1-tf-modules.git//modules/app?ref=v2.0.0"
 
  # Other inputs
  # ...
}
```

**26.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "update app module version to v2.0.0" && \
git push
``` 

**27.** Navigate to **dev-app** Terraform Cloud workspace interface. You see a new run automatically triggered with the commit message **update  app module version to v2.0.0** in step **26**. Click **See details**.

![00017](/images/5/6/00017.svg?featherlight=false&width=100pc)

**28.** Wait until the plan is finished. After that, review the plan. 

![00018](/images/5/6/00018.svg?featherlight=false&width=100pc)

**29.** If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![00019](/images/5/6/00019.svg?featherlight=false&width=100pc)

**30.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you. 

![00020](/images/5/6/00020.svg?featherlight=false&width=100pc)

**31.** After Terraform has done the applying process, check your Application Load Balancer to see whether it still works. For instance, mine is [app.tulna07.site](https://app.tulna07.site/). You are now unable to access your Application Load Balancer.

![00021](/images/5/6/00021.svg?featherlight=false&width=100pc)