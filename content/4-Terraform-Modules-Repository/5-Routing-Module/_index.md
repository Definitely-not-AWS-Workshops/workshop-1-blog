---
title : "Routing Module"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 4.5 </b> "
---

The routing module that you are going to implement has the following folder structure:

```git
routing/
│
├── route53/
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the submodules and configuration files required for the routing module.

**1.** The *routing/route53* submodule is defined by the following structure:

```git
routing/
│
├── route53/
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *routing/route53/variables.tf*:

```hcl
variable "hosted_zone_name" {
  description = "The name of the hosted zone"
  type        = string
}
```

Fill the following lines of code to *routing/route53/main.tf*:

```hcl
resource "aws_route53_zone" "main" {
  name = var.hosted_zone_name
}
```

Fill the following lines of code to *routing/route53/outputs.tf*:

```hcl
output "name_servers" {
  description = "The name servers of the hosted zone"
  value       = aws_route53_zone.main.name_servers
}

output "zone_id" {
  description = "The id of the hosted zone"
  value       = aws_route53_zone.main.zone_id
}
```

**2.** You now implement the Terraform configuration files for routing module.

Fill the following lines of code to *routing/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the routing resources delploy"
  type        = string
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "hosted_zone_name" {
  description = "The name of the Route53 hosted zone"
  type        = string
}
```

Fill the following lines of code to *routing/main.tf*:

```hcl
module "route53" {
  source = "./route53"

  hosted_zone_name = var.hosted_zone_name
}
```

Fill the following lines of code to *routing/outputs.tf*:

```hcl
output "hosted_zone_name_servers" {
  description = "The name servers of the Route53 hosted zone"
  value       = module.route53.name_servers
}

output "hosted_zone_id" {
  description = "The id of the Route53 hosted zone"
  value       = module.route53.zone_id
}
```

**3.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add routing module" && \
git push
```