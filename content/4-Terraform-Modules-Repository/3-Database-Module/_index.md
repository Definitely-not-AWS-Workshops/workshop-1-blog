---
title : "Database Module"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

The database module that you are going to implement has the following folder structure:

```git
database/
│   
└── dynamodb/
```

You then start building the *database/dynamodb* submodule required for the database module.

**1.** Do the following instructions to create the module.

The *database/dynamodb* submodule is defined by the following structure:

```git
database/
│
└── dynamodb/
    │
    ├── main.tf
    │ 
    ├── outputs.tf
    │
    └── variables.tf
```

Fill the following lines of code to *database/dynamodb/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the database resources delploy"
  type        = string
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "table_name" {
  description = "The name of the DynamoDB table"
  type        = string
}

variable "read_capacity" {
  description = "Number of read units for the table"
  type        = number
  default     = 1
}

variable "write_capacity" {
  description = "Number of write units for the table"
  type        = number
  default     = 1
}


variable "enable_deletion_protection" {
  description = "Enables deletion protection for table"
  type        = bool
  default     = false
}
```

Fill the following lines of code to *database/dynamodb/main.tf*:

```hcl
resource "aws_dynamodb_table" "main" {
  name           = var.table_name
  billing_mode   = "PROVISIONED"
  read_capacity  = var.read_capacity
  write_capacity = var.write_capacity

  hash_key = "id"

  attribute {
    name = "id"
    type = "S"
  }

  deletion_protection_enabled = var.enable_deletion_protection
}

```

Fill the following lines of code to *database/dynamodb/outputs.tf*:

```hcl
output "table_arn" {
  description = "The arn of the dynamodb table"
  value       = aws_dynamodb_table.main.arn
}
```

**2.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add database module" && \
git push
```