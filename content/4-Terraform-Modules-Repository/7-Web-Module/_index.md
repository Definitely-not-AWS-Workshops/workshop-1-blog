---
title : "Web Module"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 4.7 </b> "
---

The web module that you are going to implement has the following folder structure:

```git
web/
│
├── cloudfront/
│
├── s3-web/
│   
├── locals.tf
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the submodules and configuration files required for the web module.

**1.** The *web/cloudfront* submodule is defined by the following structure:

```git
web/
│
├── cloudfront/
│   │
│   ├── locals.tf
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *web/cloudfront/locals.tf*:

```hcl
locals {
  s3_origin_id               = "S3WebOrigin"
  alb_origin_id              = "AlbAppOrigin"
  caching_disabled_policy_id = "4135ea2d-6df8-44a3-9df3-4b5a84be39ad"
}
```

Fill the following lines of code to *web/cloudfront/variables.tf*:

```hcl
variable "bucket_regional_domain_name" {
  description = "The regional domain name of the bucket"
  type        = string
}

variable "alb_domain_name" {
  description = "The domain name of the alb"
  type        = string
}

variable "domain_name" {
  description = "The domain name for the CloudFront distribution"
  type        = string
}

variable "hosted_zone_id" {
  description = "The id of the Route53 hosted zone"
  type        = string
}

variable "certificate_arn" {
  description = "The arn of the certificate"
  type        = string
}

```

Fill the following lines of code to *web/cloudfront/main.tf*:

```hcl
resource "aws_cloudfront_distribution" "main" {
  origin {
    origin_id                = local.s3_origin_id
    domain_name              = var.bucket_regional_domain_name
    origin_access_control_id = aws_cloudfront_origin_access_control.main.id
  }

  origin {
    origin_id   = local.alb_origin_id
    domain_name = var.alb_domain_name
    custom_origin_config {
      http_port              = 80
      https_port             = 443
      origin_protocol_policy = "match-viewer"
      origin_ssl_protocols   = ["TLSv1.2", "TLSv1.1"]
    }
  }

  enabled             = true
  default_root_object = "index.html"
  aliases             = [var.domain_name]

  default_cache_behavior {
    target_origin_id = local.s3_origin_id
    cache_policy_id  = aws_cloudfront_cache_policy.s3_origin.id

    allowed_methods        = ["GET", "HEAD"]
    cached_methods         = ["GET", "HEAD"]
    viewer_protocol_policy = "redirect-to-https"
    compress               = true
  }

  ordered_cache_behavior {
    path_pattern     = "/api*"
    target_origin_id = local.alb_origin_id
    cache_policy_id  = local.caching_disabled_policy_id

    allowed_methods        = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
    cached_methods         = ["GET", "HEAD"]
    compress               = true
    viewer_protocol_policy = "redirect-to-https"
  }

  restrictions {
    geo_restriction {
      restriction_type = "none"
      locations        = []
    }
  }

  viewer_certificate {
    acm_certificate_arn      = var.certificate_arn
    minimum_protocol_version = "TLSv1.2_2021"
    ssl_support_method       = "sni-only"
  }
}

resource "aws_cloudfront_cache_policy" "s3_origin" {
  name    = "s3-origin-cloudfront-cache-policy"
  comment = "CloudFront cache policy for s3 origin"

  default_ttl = 1800
  max_ttl     = 86400
  min_ttl     = 0

  parameters_in_cache_key_and_forwarded_to_origin {
    cookies_config {
      cookie_behavior = "none"
      cookies {
        items = []
      }
    }
    headers_config {
      header_behavior = "none"
      headers {
        items = []
      }
    }
    query_strings_config {
      query_string_behavior = "none"
      query_strings {
        items = []
      }
    }

    enable_accept_encoding_brotli = true
    enable_accept_encoding_gzip   = true
  }
}

resource "aws_route53_record" "main" {
  zone_id = var.hosted_zone_id
  name    = var.domain_name
  type    = "A"

  alias {
    name                   = aws_cloudfront_distribution.main.domain_name
    zone_id                = aws_cloudfront_distribution.main.hosted_zone_id
    evaluate_target_health = false
  }
}

resource "aws_cloudfront_origin_access_control" "main" {
  name                              = var.bucket_regional_domain_name
  origin_access_control_origin_type = "s3"
  signing_behavior                  = "always"
  signing_protocol                  = "sigv4"
}
```

Fill the following lines of code to *web/cloudfront/outputs.tf*:

```hcl
output "arn" {
  description = "The arn of cloudfront"
  value       = aws_cloudfront_distribution.main.arn
}
```

**2.** The *web/s3-web* submodule is defined by the following structure:

```git
web/
│
├── s3-web/
│   │
│   ├── allow-cloudfront-policy.tpl
│   │
│   ├── dependencies.tf
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *web/s3-web/allow-cloudfront-policy.tpl*:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "${s3_bucket_arn}/*",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "${cloudfront_arn}"
        }
      }
    }
  ]
}
```

Fill the following lines of code to *web/s3-web/dependencies.tf*:

```hcl
data "aws_caller_identity" "current" {
  count = var.enable_bucket_versioning ? 1 : 0
}
```

Fill the following lines of code to *web/s3-web/variables.tf*:

```hcl
variable "bucket_name" {
  description = "The name of the bucket"
  type        = string
}

variable "cloudfront_arn" {
  description = "The arn of cloudfront"
  type        = string
}

variable "enable_bucket_versioning" {
  description = "Enable bucket versioning to keep versions of the web"
  type        = bool
  default     = false
}

variable "mfa_code" {
  description = "The mfa code required if enabling bucket versioning"
  type        = string
  sensitive   = true
  default     = ""
}

variable "force_destroy" {
  description = "If true, remove all items in the bucket and then remove the bucket"
  type = bool
  default = false
}
```

Fill the following lines of code to *web/s3-web/main.tf*:

```hcl
resource "aws_s3_bucket" "main" {
  bucket = var.bucket_name
  force_destroy = var.force_destroy
}

resource "aws_s3_bucket_policy" "allow_cloudfront" {
  bucket = aws_s3_bucket.main.id
  policy = templatefile("${path.module}/allow-cloudfront-policy.tpl", {
    s3_bucket_arn  = aws_s3_bucket.main.arn,
    cloudfront_arn = var.cloudfront_arn
  })
}

resource "aws_s3_bucket_website_configuration" "main" {
  bucket = aws_s3_bucket.main.id

  index_document {
    suffix = "index.html"
  }

  error_document {
    key = "error.html"
  }
}

resource "aws_s3_bucket_versioning" "main" {
  count  = var.enable_bucket_versioning ? 1 : 0
  bucket = aws_s3_bucket.main.id

  expected_bucket_owner = data.aws_caller_identity.current[0].account_id
  mfa                   = var.mfa_code

  versioning_configuration {
    status     = "Enabled"
    mfa_delete = "Enabled"
  }
}
```

Fill the following lines of code to *web/s3-web/outputs.tf*:

```hcl
output "bucket_regional_domain_name" {
  description = "The regional domain name of the bucket"
  value       = aws_s3_bucket.main.bucket_regional_domain_name
}

output "bucket_arn" {
  description = "The arn of bucket"
  value       = aws_s3_bucket.main.arn
}
```

**3.** You now implement the Terraform configuration files for web module.

Fill the following lines of code to *web/locals.tf*:

```hcl
locals {
  resource_name = "${var.environment}-${var.project_name}"
}
```

Fill the following lines of code to *web/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the web delploys"
  type        = string
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "enable_bucket_versioning" {
  description = "Enable bucket versioning to keep versions of the web"
  type        = bool
  default     = false
}

variable "hosted_zone_id" {
  description = "The id of the Route53 hosted zone"
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
  description = "The GitHub organization that the Github Actions role trusts"
  type        = string
}

variable "github_repo" {
  description = "The GitHub repository that the Github Actions role trusts"
  type        = string
}

variable "mfa_code" {
  description = "The mfa code required if enabling bucket versioning"
  type        = string
  sensitive   = true
  default     = ""
}


variable "gh_oidc_provider_arn" {
  description = "The arn of GitHub openid connect provider"
  type        = string
}

variable "s3_bucket_force_destroy" {
  description = "If true, remove all items in the bucket and then remove the bucket"
  type = bool
  default = false
}
```

Fill the following lines of code to *web/main.tf*:

```hcl
#----------------------------------------------------------------------
# TSL Certificate 
#----------------------------------------------------------------------
module "certificate" {
  source = "../security/certificate"

  domain_name    = var.domain_name
  hosted_zone_id = var.hosted_zone_id
}

#----------------------------------------------------------------------
# CloudFront
#----------------------------------------------------------------------
module "cloudfront" {
  source = "./cloudfront"

  bucket_regional_domain_name = module.s3_web.bucket_regional_domain_name
  domain_name                 = var.domain_name
  alb_domain_name = var.alb_domain_name
  hosted_zone_id              = var.hosted_zone_id
  certificate_arn             = module.certificate.arn
}

#----------------------------------------------------------------------
# S3 bucket for hosting ReactJS website
#----------------------------------------------------------------------
module "s3_web" {
  source = "./s3-web"

  bucket_name              = local.resource_name
  cloudfront_arn           = module.cloudfront.arn
  enable_bucket_versioning = var.enable_bucket_versioning
  mfa_code                 = var.mfa_code
  force_destroy = var.s3_bucket_force_destroy
}

#----------------------------------------------------------------------
# GitHub Actions role for deploying web
#----------------------------------------------------------------------
module "gha_role" {
  source = "../security/gha-role"

  gh_oidc_provider_arn = var.gh_oidc_provider_arn

  role_name   = "gha-role-for-deploying-web"
  github_repo = var.github_repo
  github_org  = var.github_org

  policy_arn = aws_iam_policy.allow_deploy_web.arn
}

resource "aws_iam_policy" "allow_deploy_web" {
  name        = "AllowDeployWeb"
  description = "IAM policy for deploying static website to S3 bucket"

  policy = templatefile("${path.module}/allow-deploy-web-policy.tpl", {
    s3_bucket_arn = module.s3_web.bucket_arn
  })
}
```

Fill the following lines of code to *web/outputs.tf*:

```hcl
output "gha_role_deploy_web_arn" {
  description = "The role arn for GitHub Actions that deploys web"
  value       = module.gha_role.arn
}
```

**4.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add web module" && \
git push
```