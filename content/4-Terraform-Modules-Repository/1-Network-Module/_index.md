---
title : "Network Module"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

The network module that you are going to implement has the following folder structure:

```git
network/
│
├── vpc/
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the submodules and configuration files required for the network module. 

**1.** The *network/vpc* submodule is defined by the following structure:

```git
network/
│
├── vpc/
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

Fill the following lines of code to *network/vpc/locals.tf*:

```hcl
locals {
  has_public_subnet = var.public_subnets != null && length(var.public_subnets) > 0
  all_ips           = "0.0.0.0/0"
}
```

Fill the following lines of code to *network/vpc/variables.tf*:

```hcl
variable "name" {
  description = "The name of the vpc"
  type        = string
}

variable "environment" {
  description = "The environment to which the vpc delploys"
  type        = string
}

variable "vpc_cidr" {
  description = "The cidr block of the vpc"
  type        = string
}

variable "azs" {
  description = "The choosen azs to allocate resources in the vpc"
  type        = list(string)
  default     = []
}

variable "public_subnets" {
  description = "The cidr blocks for the public subnets"
  type = list(object({
    cidr = string,
    tier = optional(string)
  }))
  default = []
}

variable "public_subnet_prefix" {
  description = "Prefix to append to public subnets name"
  type        = string
  default     = "public"
}

variable "private_subnets" {
  description = "The cidr blocks for the private subnets"
  type = list(object({
    cidr = string,
    tier = optional(string)
  }))
  default = []
}

variable "private_subnet_prefix" {
  description = "Prefix to append to private subnets name"
  type        = string
  default     = "private"
}

```

Fill the following lines of code to *network/vpc/main.tf*:

```hcl
#----------------------------------------------------------------------
# Vpc
#----------------------------------------------------------------------
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = var.name
  }
}

#----------------------------------------------------------------------
# Internet Gateway
#----------------------------------------------------------------------
resource "aws_internet_gateway" "main" {
  count = local.has_public_subnet ? 1 : 0
}

resource "aws_internet_gateway_attachment" "main" {
  count = local.has_public_subnet ? 1 : 0

  vpc_id              = aws_vpc.main.id
  internet_gateway_id = aws_internet_gateway.main[0].id
}

#----------------------------------------------------------------------
# Subnets
#----------------------------------------------------------------------
resource "aws_subnet" "public" {
  count = length(var.public_subnets)

  vpc_id                  = aws_vpc.main.id
  availability_zone       = element(var.azs, count.index)
  cidr_block              = var.public_subnets[count.index].cidr
  map_public_ip_on_launch = true

  tags = {
    Name = "${var.name}-${var.public_subnet_prefix}-${element(var.azs, count.index)}"
    Tier = coalesce(var.public_subnets[count.index].tier, var.public_subnet_prefix)
    Type = var.public_subnet_prefix
  }
}

resource "aws_subnet" "private" {
  count = length(var.private_subnets)

  vpc_id            = aws_vpc.main.id
  availability_zone = element(var.azs, count.index)
  cidr_block        = var.private_subnets[count.index].cidr

  tags = {
    Name = "${var.name}-${var.private_subnet_prefix}-${element(var.azs, count.index)}"
    Tier = coalesce(var.private_subnets[count.index].tier, var.private_subnet_prefix)
    Type = var.private_subnet_prefix
  }
}

#----------------------------------------------------------------------
# Route Tables
#----------------------------------------------------------------------
resource "aws_route_table" "public" {
  count = local.has_public_subnet ? 1 : 0

  vpc_id = aws_vpc.main.id

  tags = {
    Name = "public-rtb"
  }
}

resource "aws_route" "to_igw" {
  count = local.has_public_subnet ? 1 : 0

  route_table_id         = aws_route_table.public[0].id
  destination_cidr_block = local.all_ips
  gateway_id             = aws_internet_gateway.main[0].id
}

resource "aws_route_table_association" "public" {
  count          = length(aws_subnet.public)
  route_table_id = aws_route_table.public[0].id
  subnet_id      = aws_subnet.public[count.index].id
}

resource "aws_route_table" "private" {
  count  = length(var.private_subnets) / length(var.azs)
  vpc_id = aws_vpc.main.id
}

resource "aws_route_table_association" "private" {
  count          = length(aws_subnet.private)
  route_table_id = element(aws_route_table.private[*].id, floor(length(aws_route_table.private) / length(var.azs)))
  subnet_id      = aws_subnet.private[count.index].id
}

```

Fill the following lines of code to *network/vpc/outputs.tf*:

```hcl
output "vpc_id" {
  description = "The id of the vpc"
  value       = aws_vpc.main.id
}

output "vpc_arn" {
  description = "The arn of the vpc"
  value       = aws_vpc.main.arn
}

output "public_subnet_ids" {
  description = "The ids of the public subnets"
  value       = aws_subnet.public[*].id
}

output "private_subnet_ids" {
  description = "The ids of the private subnets"
  value       = aws_subnet.private[*].id
}

output "subnet_ids" {
  description = "The ids of the subnets"
  value       = concat(aws_subnet.public[*].id, aws_subnet.private[*].id)
}

output "public_route_table_id" {
  description = "The id of the public route table"
  value       = aws_route_table.public[0].id
}
```

**2.** You now implement the Terraform configuration files for network module.

Fill the following lines of code to *network/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the network resources delploy"
  type        = string
  default     = "dev"
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "vpc_cidr" {
  description = "The cidr block of the vpc"
  type        = string
}

variable "azs" {
  description = "The choosen azs to allocate resources in the vpc"
  type        = list(string)
  default     = []
}

variable "public_subnets" {
  description = "The cidr blocks for the public subnets"
  type = list(object({
    cidr = string,
    tier = optional(string)
  }))
  default = []
}

variable "public_subnet_prefix" {
  description = "Prefix to append to public subnets name"
  type        = string
  default     = "public"
}

variable "private_subnets" {
  description = "The cidr blocks for the private subnets"
  type = list(object({
    cidr = string,
    tier = optional(string)
  }))
  default = []
}

variable "private_subnet_prefix" {
  description = "Prefix to append to private subnets name"
  type        = string
  default     = "private"
}
```

Fill the following lines of code to *network/main.tf*:

```hcl
module "vpc" {
  source = "./vpc"

  name        = var.project_name
  environment = var.environment

  vpc_cidr = var.vpc_cidr
  azs      = var.azs

  public_subnets        = var.public_subnets
  public_subnet_prefix  = var.public_subnet_prefix
  private_subnets       = var.private_subnets
  private_subnet_prefix = var.private_subnet_prefix
}
```

Fill the following lines of code to *network/outputs.tf*:

```hcl
output "vpc_id" {
  description = "The id of the vpc"
  value       = module.vpc.vpc_id
}

output "vpc_arn" {
  description = "The arn of the vpc"
  value       = module.vpc.vpc_arn
}

output "public_subnet_ids" {
  description = "The ids of the public subnets"
  value       = module.vpc.public_subnet_ids
}

output "private_subnet_ids" {
  description = "The ids of the private subnets"
  value       = module.vpc.private_subnet_ids
}

output "subnet_ids" {
  description = "The ids of the subnets"
  value       = module.vpc.subnet_ids
}
```

**3.** Remove the *.gitkeep* file.

```git
rm .gitkeep
```

**4.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "remove .gitkeep and add network module" && \
git push
```