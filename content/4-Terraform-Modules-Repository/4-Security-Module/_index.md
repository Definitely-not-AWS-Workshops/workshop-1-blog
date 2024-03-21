---
title : "Security Module"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---

The security module that you are going to implement has the following folder structure:

```git
security/
│
├── certificate/
│   
├── gha-role/
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the submodules and configuration files required for the security module.

**1.** The *security/certificate* submodule is defined by the following structure:

```git
security/
│
├── certificate/
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *security/certificate/variables.tf*:

```hcl
variable "hosted_zone_id" {
  description = "The id of the Route53 hosted zone"
  type        = string
}

variable "domain_name" {
  description = "The domain name of the website"
  type        = string
}
```

Fill the following lines of code to *security/certificate/main.tf*:

```hcl
resource "aws_acm_certificate" "main" {
  domain_name       = var.domain_name
  validation_method = "DNS"
}

resource "aws_route53_record" "main" {
  for_each = {
    for dvo in aws_acm_certificate.main.domain_validation_options : dvo.domain_name => {
      name   = dvo.resource_record_name
      record = dvo.resource_record_value
      type   = dvo.resource_record_type
    }
  }

  allow_overwrite = true
  name            = each.value.name
  records         = [each.value.record]
  ttl             = 60
  type            = each.value.type
  zone_id         = var.hosted_zone_id
}

resource "aws_acm_certificate_validation" "main" {
  certificate_arn         = aws_acm_certificate.main.arn
  validation_record_fqdns = [for record in aws_route53_record.main : record.fqdn]
}
```

Fill the following lines of code to *security/certificate/outputs.tf*:

```hcl
output "arn" {
  description = "The arn of the certificate"
  value       = aws_acm_certificate_validation.main.certificate_arn
}
```

**2.** The *security/gha-role* submodule is defined by the following structure:

```git
security/
│
├── gha-role/
│   │
│   ├── gha-trust-policy.tpl
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *security/gha-role/gha-trust-policy.tpl*:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "${gh_oidc_provider_arn}"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:${github_org}/${github_repo}:*"
        },
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        }
      }
    }
  ]
}
```

Fill the following lines of code to *security/gha-role/variables.tf*:

```hcl
variable "gh_oidc_provider_arn" {
  description = "The arn of GitHub openid connect provider"
  type        = string
}

variable "role_name" {
  description = "The name of the role"
  type        = string
}

variable "policy_arn" {
  description = "The policy arn for the role"
  type        = string
}

variable "github_org" {
  description = "The GitHub organization that the Github Actions role trusts"
  type        = string
}

variable "github_repo" {
  description = "The GitHub repository that the Github Actions role trusts"
  type        = string
}
```

Fill the following lines of code to *security/gha-role/main.tf*:

```hcl
resource "aws_iam_role" "github_actions" {
  name = var.role_name
  assume_role_policy = templatefile("${path.module}/gha-trust-policy.tpl", {
    gh_oidc_provider_arn = var.gh_oidc_provider_arn,
    github_org           = var.github_org,
    github_repo          = var.github_repo
  })
  managed_policy_arns = [var.policy_arn]
}
```

Fill the following lines of code to *security/gha-role/outputs.tf*:

```hcl
output "arn" {
  description = "The role arn to use for GitHub Actions"
  value       = aws_iam_role.github_actions.arn
}

output "name" {
  description = "The role name to use for GitHub Actions"
  value       = aws_iam_role.github_actions.name
}
```

**3.** You now implement the Terraform configuration files for security module.

Fill the following lines of code to *security/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the security resources delploy"
  type        = string
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}
```

Fill the following lines of code to *security/main.tf*:

```hcl
resource "aws_iam_openid_connect_provider" "main" {
  url             = "https://token.actions.githubusercontent.com"
  thumbprint_list = ["1b511abead59c6ce207077c0bf0e0043b1382612"]
  client_id_list = [
    "sts.amazonaws.com",
  ]
}
```

Fill the following lines of code to *security/outputs.tf*:

```hcl
output "gh_oidc_provider_arn" {
  value       = aws_iam_openid_connect_provider.main.arn
  description = "The arn of Github openid connect provider"
}
```


**4.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add security module" && \
git push
```