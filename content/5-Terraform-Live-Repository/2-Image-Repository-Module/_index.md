---
title : "Image Repository Module"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---

The image repository module that you are going to implement has the following folder structure:

```git
image-repository/
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

You then start building the configuration files required for the image repository module and triggering the first Terraform Cloud run for the module manually. 

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *image-repository/versions.tf*:

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

Fill the following lines of code to *image-repository/state.tf*:

```hcl
terraform {
  cloud {
    organization = "aws-first-cloud-journey"

    workspaces {
      name    = "dev-image-repository"
      project = "workshop-1"
    }
  }
}
```

Fill the following lines of code to *image-repository/variables.tf*:

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

variable "force_delete" {
  description = "If true,  delete the repository even if it contains images"
  type        = bool
  default     = false
}
```

Fill the following lines of code to *image-repository/terraform.tfvars*:

```hcl
region = "us-east-1"
environment = "dev"
project_name = "workshop-1"

# Disable in real usage
force_delete = true
```

Fill the following lines of code to *image-repository/providers.tf*:

```hcl
provider "aws" {
  region = var.region
}
```

Fill the following lines of code to *image-repository/main.tf*. Remember to replace *\<your-github-username\>* with your GitHub username:

```hcl
module "image_repository" {
  source = "git::https://github.com/<your-github-username>/workshop-1-tf-modules.git//modules/image-repository?ref=v1.0.0"

  environment  = var.environment
  project_name = var.project_name

  force_delete = var.force_delete
}
```

Fill the following lines of code to *image-repository/outputs.tf*:

```hcl
output "repository_name" {
  description = "The name of the repository"
  value       = module.image_repository.repository_name
}

output "repository_url" {
  description = "The url of the repository"
  value       = module.image_repository.repository_url
}

output "registry_id" {
  description = "The registry ID where the repository was created"
  value       = module.image_repository.registry_id
}

output "repository_arn" {
  description = "The arn of the repo"
  value       = module.image_repository.repository_arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add image repository module" && \
git push
```

**3.** Navigate to your **dev-image-repository** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/5/2/0001.svg?featherlight=false&width=100pc)

**4.** Scroll down to **Remote state sharing** section. Select **Share with specific workspaces**. Click the **Select workspaces to share with** dropdown and choose **dev-app**. Click **Save settings**.

![0002](/images/5/2/0002.svg?featherlight=false&width=100pc)

**5.** Back to **dev-image-repository** Terraform Cloud workspace interface. Click **New run**.

![0003](/images/5/2/0003.svg?featherlight=false&width=100pc)

**6.** Click **Start**.

![0004](/images/5/2/0004.svg?featherlight=false&width=100pc)

**7.** Wait until the plan is finished. After that, review the plan.

![0005](/images/5/2/0005.svg?featherlight=false&width=100pc)

**8.** If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0006](/images/5/2/0006.svg?featherlight=false&width=100pc)

**9.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and provision AWS resources for you.

![0007](/images/5/2/0007.svg?featherlight=false&width=100pc)

**10.** After Terraform has done the applying process, you may access your AWS account to view the Terraform-provided AWS resources. 

![0008](/images/5/2/0008.svg?featherlight=false&width=100pc)

**11.** Go to [AWS ECR console](https://console.aws.amazon.com/ecr/).

**12.** In the left sidebar, click **Repositories** under the **Private registry** dropdown to check out your newly created repository.

![0009](/images/5/2/0009.svg?featherlight=false&width=100pc)

**13.** Turn on your [Docker Desktop](https://www.docker.com/products/docker-desktop/). You next push a small Docker image that print the IP address for testing the app tier.

![00010](/images/5/2/00010.svg?featherlight=false&width=100pc)

**14.** Clone this [repository](https://github.com/Definitely-not-AWS-Workshops/ip-printer.git).

```git
git clone https://github.com/Definitely-not-AWS-Workshops/ip-printer.git
```

**15.** Move inside the local repository you have just cloned.

```git
cd ip-printer
```

**16.** Go to your AWS account homepage. Click the AWS username dropdown and then get your AWS account ID.

![00011](/images/5/2/00011.svg?featherlight=false&width=100pc)

**17.** Retrieve an authentication token and authenticate your Docker client to your registry. Replace *\<your-aws-account-id\>* with your AWS account ID from step **16**.

```git
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com
```

**18.** Build your Docker image using the following command.

```git
docker build -t workshop-1 .
```

**19.** After the build completes, tag your image so you can push the image to this repository. Replace *\<your-aws-account-id\>* with your AWS account ID from step **16**.

```git
docker tag workshop-1:latest <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/workshop-1:v0.0.0
```

**20.** Run the following command to push this image to your newly created AWS ECR repository. Replace *\<your-aws-account-id\>* with your AWS account ID from step **16**.

```git
docker push <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/workshop-1:v0.0.0
```

**21.** Go to [AWS ECR console](https://console.aws.amazon.com/ecr).

**22.** In the left sidebar, click **Repositories** under the **Private registry** dropdown. Click the *workshop-1* image repository.

![00012](/images/5/2/0009.svg?featherlight=false&width=100pc)

**23.** Click **Images**. You successfully push the first image to the AWS ECR repository.

![00014](/images/5/2/00012.svg?featherlight=false&width=100pc)


