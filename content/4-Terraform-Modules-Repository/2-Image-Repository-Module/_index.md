---
title : "Image Repository Module"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

The image repository module that you are going to implement has the following folder structure:

```git
image-repository/
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the configuration files required for the image repository module.

**1.** Do the following instructions to create Terraform configurations for the module.

Fill the following lines of code to *image-repository/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the repository delploys"
  type        = string
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

Fill the following lines of code to *image-repository/main.tf*:

```hcl
resource "aws_ecr_repository" "main" {
  name                 = var.project_name
  image_tag_mutability = "IMMUTABLE"

  encryption_configuration {
    encryption_type = "AES256"
  }

  force_delete = var.force_delete
}

resource "aws_ecr_registry_scanning_configuration" "main" {
  scan_type = "BASIC"
  rule {
    scan_frequency = "SCAN_ON_PUSH"

    repository_filter {
      filter      = "*"
      filter_type = "WILDCARD"
    }
  }
}

```

Fill the following lines of code to *image-repository/outputs.tf*:

```hcl
output "repository_name" {
  description = "The name of the repository"
  value       = var.project_name
}

output "repository_url" {
  description = "The url of the repository"
  value       = aws_ecr_repository.main.repository_url
}

output "registry_id" {
  description = "The registry ID where the repository was created"
  value       = aws_ecr_repository.main.registry_id
}

output "repository_arn" {
  description = "The arn of the repo"
  value       = aws_ecr_repository.main.arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add image repository module" && \
git push
```