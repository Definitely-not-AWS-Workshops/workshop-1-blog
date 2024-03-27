---
title : "App Module"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 4.6 </b> "
---

The app module that you are going to implement has the following folder structure:

```git
app/
│
├── alb/
│
├── asg/
│
├── fargate-app/
│   
├── allow-deploy-app-policy.tpl
│   
├── dependencies.tf
│   
├── locals.tf
│   
├── main.tf
│   
├── outputs.tf
│   
└── variables.tf
```

You then start building the submodules and configuration files required for the app module.

**1.** The *app/alb* submodule is defined by the following structure:

```git
app/
│
├── alb/
│   │
│   ├── main.tf
│   │ 
│   ├── outputs.tf
│   │
│   └── variables.tf
└── ...
```

Fill the following lines of code to *app/alb/variables.tf*:

```hcl
variable "name" {
  description = "The name of the alb"
  type        = string
}

variable "domain_name" {
  description = "The domain name of the alb"
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

variable "vpc_id" {
  description = "The id of the vpc"
  type        = string
}

variable "subnet_ids" {
  description = "The subnet ids of the alb"
  type        = list(string)
}
```

Fill the following lines of code to *app/alb/main.tf*:

```hcl
resource "aws_lb" "main" {
  name = var.name

  load_balancer_type = "application"
  subnets            = var.subnet_ids
  security_groups    = [aws_security_group.alb.id]
}

resource "aws_lb_listener" "main" {
  load_balancer_arn = aws_lb.main.arn

  port            = "443"
  protocol        = "HTTPS"
  certificate_arn = var.certificate_arn
  ssl_policy      = "ELBSecurityPolicy-TLS13-1-2-2021-06"

  default_action {
    type = "fixed-response"
    fixed_response {
      content_type = "text/plain"
      message_body = "404: page not found"
      status_code  = 404
    }
  }
}

resource "aws_lb_listener_rule" "main" {
  listener_arn = aws_lb_listener.main.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.main.arn
  }

  condition {
    path_pattern {
      values = ["/", "/api/anime"]
    }
  }
}

resource "aws_route53_record" "main" {
  zone_id = var.hosted_zone_id
  name    = var.domain_name
  type    = "A"

  alias {
    name                   = aws_lb.main.dns_name
    zone_id                = aws_lb.main.zone_id
    evaluate_target_health = false
  }
}

resource "aws_lb_target_group" "main" {
  name   = var.name
  vpc_id = var.vpc_id

  target_type      = "ip"
  port             = 80
  protocol         = "HTTP"
  protocol_version = "HTTP1"

  health_check {
    path                = "/health"
    protocol            = "HTTP"
    matcher             = "200"
    interval            = 15
    timeout             = 3
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }
}

resource "aws_security_group" "alb" {
  name   = "${var.name}-sg-alb"
  vpc_id = var.vpc_id
}
```

Fill the following lines of code to *app/alb/outputs.tf*:

```hcl
output "target_group_arn" {
  description = "The arn of the alb targetgroup"
  value       = aws_lb_target_group.main.arn
}

output "security_group_id" {
  description = "The security group id of the alb"
  value       = aws_security_group.alb.id
}
```

**2.** The *app/asg* submodule is defined by the following structure:

```git
app/
│
├── asg/
│   │
│   ├── main.tf
│   │ 
│   └── variables.tf
└── ...
```

Fill the following lines of code to *app/asg/variables.tf*:

```hcl
variable "ecs_cluster_name" {
  description = "The name of the ecs cluster"
  type        = string
}

variable "ecs_service_name" {
  description = "The name of the ecs service"
  type        = string
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

Fill the following lines of code to *app/asg/main.tf*:

```hcl
resource "aws_appautoscaling_target" "main" {
  max_capacity       = var.max_capacity
  min_capacity       = var.min_capacity
  resource_id        = "service/${var.ecs_cluster_name}/${var.ecs_service_name}"
  scalable_dimension = "ecs:service:DesiredCount"
  service_namespace  = "ecs"
}

resource "aws_appautoscaling_policy" "main" {
  name               = "app"
  policy_type        = "TargetTrackingScaling"
  resource_id        = aws_appautoscaling_target.main.resource_id
  scalable_dimension = aws_appautoscaling_target.main.scalable_dimension
  service_namespace  = aws_appautoscaling_target.main.service_namespace

  target_tracking_scaling_policy_configuration {
    target_value       = 70
    scale_in_cooldown  = 300
    scale_out_cooldown = 300

    predefined_metric_specification {
      predefined_metric_type = "ECSServiceAverageCPUUtilization"
    }
  }
}
```

**3.** The *app/fargate-app* submodule is defined by the following structure:

```git
app/
│
├── fargate-app/
│   │
│   ├── allow-to-communicate-with-dynamodb-policy.tpl
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

Fill the following lines of code to *app/fargate-app/allow-to-communicate-with-dynamodb-policy.tpl*:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:Query",
                "dynamodb:Scan"
            ],
            "Resource": "${dynamodb_table_arn}"
        }
    ]
}
```

Fill the following lines of code to *app/fargate-app/dependencies.tf*:

```hcl
data "aws_iam_role" "ecs_task_execution" {
  name = "ecsTaskExecutionRole"
}

data "aws_ecs_task_definition" "main" {
  task_definition = aws_ecs_task_definition.main.family
  depends_on      = [aws_ecs_task_definition.main]
}
```

Fill the following lines of code to *app/fargate-app/variables.tf*:

```hcl
variable "vpc_id" {
  description = "The id of the vpc"
  type        = string
}

variable "ecs_cluster_name" {
  description = "The name of the ecs cluster"
  type        = string
}

variable "ecs_service_name" {
  description = "The name of the ecs service"
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

variable "container_definitions" {
  description = "The container definitions for the task"
  type        = string
}

variable "target_group_arn" {
  description = "The arn of the target group"
  type        = string
}

variable "subnet_ids" {
  description = "The subnet ids of the app"
  type        = list(string)
}

variable "dynamodb_table_arn" {
  description = "The arn of the dynamodb table"
  type        = string
}
```

Fill the following lines of code to *app/fargate-app/main.tf*:

```hcl
resource "aws_ecs_cluster" "main" {
  name = var.ecs_cluster_name
}

resource "aws_ecs_cluster_capacity_providers" "main" {
  cluster_name       = aws_ecs_cluster.main.name
  capacity_providers = ["FARGATE"]
}

resource "aws_ecs_service" "main" {
  cluster = aws_ecs_cluster.main.id
  name    = var.ecs_service_name

  task_definition = "${aws_ecs_task_definition.main.family}:${max(aws_ecs_task_definition.main.revision, data.aws_ecs_task_definition.main.revision)}"
  desired_count   = var.desired_count

  scheduling_strategy                = "REPLICA"
  deployment_minimum_healthy_percent = 50

  capacity_provider_strategy {
    base              = 0
    weight            = 1
    capacity_provider = "FARGATE"
  }

  deployment_circuit_breaker {
    enable   = true
    rollback = true
  }

  network_configuration {
    subnets         = var.subnet_ids
    security_groups = [aws_security_group.app.id]

    # Turn on for testing with public subnets
    # assign_public_ip = true
  }

  load_balancer {
    target_group_arn = var.target_group_arn
    container_name   = "container-1"
    container_port   = 8080
  }

  lifecycle {
    ignore_changes = [desired_count]
  }

  depends_on = [data.aws_ecs_task_definition.main]
}

resource "aws_ecs_task_definition" "main" {
  family = var.ecs_service_name

  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = var.cpu
  memory                   = var.memory
  task_role_arn            = aws_iam_role.allow_to_communicate_with_dynamodb.arn
  execution_role_arn       = data.aws_iam_role.ecs_task_execution.arn

  runtime_platform {
    operating_system_family = "LINUX"
    cpu_architecture        = "X86_64"
  }

  container_definitions = var.container_definitions
}

resource "aws_iam_role" "allow_to_communicate_with_dynamodb" {
  name = "allow-to-communicate-with-dynamodb"

  assume_role_policy = jsonencode({
    "Version" : "2012-10-17",
    "Statement" : [
      {
        "Effect" : "Allow",
        "Principal" : {
          "Service" : "ecs-tasks.amazonaws.com"
        },
        "Action" : "sts:AssumeRole"
      }
    ]
  })

  managed_policy_arns = [aws_iam_policy.allow_to_communicate_with_dynamodb.arn]
}

resource "aws_iam_policy" "allow_to_communicate_with_dynamodb" {
  name        = "AllowToCommunicateWithDynamodb"
  description = "IAM policy for ECS to communicate with DynamoDB"

  policy = templatefile("${path.module}/allow-to-communicate-with-dynamodb-policy.tpl", {
    dynamodb_table_arn = var.dynamodb_table_arn,
  })
}

resource "aws_security_group" "app" {
  name   = "${var.ecs_cluster_name}-sg-app"
  vpc_id = var.vpc_id
}
```

Fill the following lines of code to *app/fargate-app/outputs.tf*:

```hcl
output "security_group_id" {
  description = "The security group id of the app"
  value       = aws_security_group.app.id
}

output "ecs_service_arn" {
  description = "The arn of the ecs service"
  value       = aws_ecs_service.main.id
}

output "ecs_cluster_name" {
  description = "The name of the ecs cluster"
  value       = aws_ecs_cluster.main.name
}

output "ecs_service_name" {
  description = "The name of the ecs service"
  value       = aws_ecs_service.main.name
}

output"allow_to_communicate_with_dynamodb_role_arn" {
  description = "The role arn for allowing to communicate with dynamodb"
  value = aws_iam_role.allow_to_communicate_with_dynamodb.arn
}
```

**4.** You now implement the Terraform configuration files for app module.

Fill the following lines of code to *app/allow-deploy-app-policy.tpl*:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "RegisterAndDescribeTaskDefinition",
      "Effect": "Allow",
      "Action": [
        "ecs:RegisterTaskDefinition",
        "ecs:DescribeTaskDefinition"
      ],
      "Resource": "*"
    },
    {
      "Sid": "PassRolesInTaskDefinition",
      "Effect": "Allow",
      "Action": [
        "iam:PassRole"
      ],
      "Resource": [
        "${ecs_execution_role_arn}",
        "${ecs_task_role_arn}"
      ]
    },
    {
      "Sid": "DeployService",
      "Effect": "Allow",
      "Action": [
        "ecs:UpdateService",
        "ecs:DescribeServices"
      ],
      "Resource": "${ecs_service_arn}"
    },
    {
      "Sid": "GetAuthorizationToken",
      "Effect": "Allow",
      "Action": "ecr:GetAuthorizationToken",
      "Resource": "*"
    },
    {
      "Sid": "AllowPush",
      "Effect": "Allow",
      "Action": [
        "ecr:CompleteLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:InitiateLayerUpload",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage"
      ],
      "Resource": "${ecr_repository_arn}"
    }
  ]
}
```

Fill the following lines of code to *app/dependencies.tf*:

```hcl
data "aws_region" "current" {}

data "aws_iam_role" "ecs_task_execution" {
  name = "ecsTaskExecutionRole"
}

data "aws_route_table" "app" {
  subnet_id = var.app_subnet_ids[0]
}
```

Fill the following lines of code to *app/variables.tf*:

```hcl
variable "environment" {
  description = "The environment to which the app delploys"
  type        = string
}

variable "project_name" {
  description = "The name of the project"
  type        = string
}

variable "hosted_zone_id" {
  description = "The id of the Route53 hosted zone"
  type        = string
}

variable "vpc_id" {
  description = "The id of the vpc"
  type        = string
}

variable "gh_oidc_provider_arn" {
  description = "The arn of GitHub openid connect provider"
  type        = string
}

variable "ecr_repository_arn" {
  description = "The arn of the ecr repository"
  type        = string
}

variable "dynamodb_table_arn" {
  description = "The arn of the dynamodb table"
  type        = string
}

variable "domain_name" {
  description = "The domain name of the alb"
  type        = string
}

variable "app_name" {
  description = "The name of the application"
  type        = string
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

variable "github_org" {
  description = "The GitHub organization that the Github Actions role trusts"
  type        = string
}

variable "github_repo" {
  description = "The GitHub repository that the Github Actions role trusts"
  type        = string
}

variable "container_definitions" {
  description = "The container definitions for the task"
  type        = string
}

variable "alb_subnet_ids" {
  description = "The subnet ids of the alb"
  type        = list(string)
}

variable "app_subnet_ids" {
  description = "The subnet ids of the app"
  type        = list(string)
}

variable "app_port" {
  description = "The port of the app"
  type        = number
}
```

Fill the following lines of code to *app/locals.tf*:

```hcl
locals {
  tcp_protocol            = "tcp"
  interface_endpoint_port = 443
  all_ips                 = "0.0.0.0/0"
  any_protocol            = "-1"

  resource_name = "${var.environment}-${var.project_name}"
}
```

Fill the following lines of code to *app/main.tf*:

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
# Application Load Balancer
#----------------------------------------------------------------------
module "alb" {
  source = "./alb"

  name        = local.resource_name
  domain_name = var.domain_name

  vpc_id     = var.vpc_id
  subnet_ids = var.alb_subnet_ids

  hosted_zone_id  = var.hosted_zone_id
  certificate_arn = module.certificate.arn
}

resource "aws_vpc_security_group_ingress_rule" "allow_all_to_alb" {
  security_group_id = module.alb.security_group_id
  cidr_ipv4         = local.all_ips
  ip_protocol       = local.any_protocol
}

resource "aws_vpc_security_group_egress_rule" "allow_all_from_alb" {
  security_group_id = module.alb.security_group_id
  cidr_ipv4         = local.all_ips
  ip_protocol       = local.any_protocol
}

#----------------------------------------------------------------------
# Auto Scaling Group
#----------------------------------------------------------------------
module "asg" {
  source = "./asg"

  ecs_cluster_name = module.fargate_app.ecs_cluster_name
  ecs_service_name = module.fargate_app.ecs_service_name

  max_capacity = var.max_capacity
  min_capacity = var.min_capacity
}

#----------------------------------------------------------------------
# Fargate App
#----------------------------------------------------------------------
module "fargate_app" {
  source = "./fargate-app"

  ecs_cluster_name = local.resource_name
  ecs_service_name = var.app_name

  vpc_id     = var.vpc_id
  subnet_ids = var.app_subnet_ids

  target_group_arn   = module.alb.target_group_arn
  dynamodb_table_arn = var.dynamodb_table_arn

  cpu           = var.cpu
  memory        = var.memory
  desired_count = var.desired_count

  container_definitions = var.container_definitions

  depends_on = [module.alb]
}

resource "aws_vpc_security_group_ingress_rule" "allow_alb_to_app" {
  security_group_id = module.fargate_app.security_group_id

  referenced_security_group_id = module.alb.security_group_id
  ip_protocol                  = local.tcp_protocol
  from_port                    = var.app_port
  to_port                      = var.app_port
}

resource "aws_vpc_security_group_egress_rule" "allow_all_from_app" {
  security_group_id = module.fargate_app.security_group_id
  cidr_ipv4         = local.all_ips
  ip_protocol       = local.any_protocol
}

#----------------------------------------------------------------------
# GitHub Actions role for deploying app
#----------------------------------------------------------------------
module "gha_role" {
  source = "../security/gha-role"

  gh_oidc_provider_arn = var.gh_oidc_provider_arn

  role_name   = "gha-role-for-deploying-app"
  github_repo = var.github_repo
  github_org  = var.github_org

  policy_arn = aws_iam_policy.allow_deploy_app.arn
}

resource "aws_iam_policy" "allow_deploy_app" {
  name        = "AllowDeployApp"
  description = "IAM policy for deploying app to fargate"

  policy = templatefile("${path.module}/allow-deploy-app-policy.tpl", {
    ecs_execution_role_arn = data.aws_iam_role.ecs_task_execution.arn,
    ecs_task_role_arn      = module.fargate_app.allow_to_communicate_with_dynamodb_role_arn,
    ecs_service_arn        = module.fargate_app.ecs_service_arn,
    ecr_repository_arn     = var.ecr_repository_arn
  })
}

# #----------------------------------------------------------------------
# # s3 Gateway Endpoint
# #----------------------------------------------------------------------
resource "aws_vpc_endpoint" "s3_gateway_endpoint" {
  vpc_id = var.vpc_id

  vpc_endpoint_type = "Gateway"
  service_name      = "com.amazonaws.${data.aws_region.current.name}.s3"
  route_table_ids   = [data.aws_route_table.app.route_table_id]

  tags = {
    Name        = "s3-gateway-endpoint"
    Environment = var.environment
  }
}

# #----------------------------------------------------------------------
# # DynamoDB Gateway Endpoint
# #----------------------------------------------------------------------
resource "aws_vpc_endpoint" "dynamodb_gateway_endpoint" {
  vpc_id = var.vpc_id

  vpc_endpoint_type = "Gateway"
  service_name      = "com.amazonaws.${data.aws_region.current.name}.dynamodb"
  route_table_ids   = [data.aws_route_table.app.route_table_id]

  tags = {
    Name        = "dynamodb-gateway-endpoint"
    Environment = var.environment
  }
}

# #----------------------------------------------------------------------
# # Security Group for Interface Endpoints allowing traffic from App
# #----------------------------------------------------------------------
resource "aws_security_group" "interface_endpoint" {
  vpc_id = var.vpc_id
}

resource "aws_vpc_security_group_ingress_rule" "allow_app" {
  security_group_id = aws_security_group.interface_endpoint.id

  referenced_security_group_id = module.fargate_app.security_group_id
  ip_protocol                  = local.tcp_protocol
  from_port                    = local.interface_endpoint_port
  to_port                      = local.interface_endpoint_port
}

# #----------------------------------------------------------------------
# # ecr api Interface Endpoint
# #----------------------------------------------------------------------
resource "aws_vpc_endpoint" "ecr_api_interface_endpoint" {
  vpc_id = var.vpc_id

  vpc_endpoint_type   = "Interface"
  private_dns_enabled = true
  service_name        = "com.amazonaws.${data.aws_region.current.name}.ecr.api"
  subnet_ids          = var.app_subnet_ids
  security_group_ids  = [aws_security_group.interface_endpoint.id]

  tags = {
    Name        = "ecr-api-interface-endpoint"
    Environment = var.environment
  }
}

# #----------------------------------------------------------------------
# # ecr dkr Interface Endpoint
# #----------------------------------------------------------------------
resource "aws_vpc_endpoint" "ecr_dkr_interface_endpoint" {
  vpc_id = var.vpc_id

  vpc_endpoint_type   = "Interface"
  private_dns_enabled = true
  service_name        = "com.amazonaws.${data.aws_region.current.name}.ecr.dkr"
  subnet_ids          = var.app_subnet_ids
  security_group_ids  = [aws_security_group.interface_endpoint.id]

  tags = {
    Name        = "ecr-dkr-interface-endpoint"
    Environment = var.environment
  }
}
```

Fill the following lines of code to *app/outputs.tf*:

```hcl
output "gha_role_deploy_app_arn" {
  description = "The role arn for GitHub Actions that deploys app"
  value       = module.gha_role.arn
}
```

**5.** Commit and push the module to the GitHub repository.

```git
git add . && \
git commit -m "add app module" && \
git push
```