---
title : "Terraform Modules"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

You should divide Terraform configuration into modules to reduce the risk of a single point of failure when placing all configurations into a single file.

Choosing which infrastructure to include is one of the most challenging parts of creating a new Terraform module. In this section, you look at the Terraform team's recommendations for module creation. You then determine our necessary module based on those recommendations and their relationships.

#### Module Creation - Recommended Pattern

The Terraform team advises considering the following three factors when building a module:

- **Encapsulation:** *Group infrastructure that is always deployed together*.
Although adding more infrastructure to a module facilitates its deployment by the end user, it also complicates the understanding of the module's requirements and goal.

- **Privileges:** *Restrict modules to privilege boundaries*.
Using a module that has infrastructure that is overseen by many groups may accidentally violate the principle of separation of responsibilities. To improve infrastructure isolation and safeguard your infrastructure, only group resources within privileged boundaries.

- **Volatility:** *Separate long-lived infrastructure from short-lived*.
Database infrastructure, for instance, is usually static, but teams may launch application servers several times a day. State-storing infrastructure is exposed to needless churn and risk when it is managed in the same module as application servers.

{{% notice info %}}
Refer to [Module creation - recommended pattern](https://developer.hashicorp.com/terraform/tutorials/modules/pattern-module-creation) for additional recommended practices regarding module creation from the team.
{{% /notice %}}

You would take the recommendations to identify the Terraform modules needed to make AnimeHub operate at scale.

#### Module Proposal

As mentioned in previous section — [2.1 AWS Architecture](../1-aws-architecture/) , AnimeHub is simply divided into three main parts: web, app, and database: 

- The **Web tier** requires an S3 bucket for static website hosting and CloudFront for content delivery.

- The **App tier** requires ECS Fargate for containerized application deployment, an Application Load Balancer for efficient traffic distribution, and an Auto Scaling Group for managing resource allocation dynamically.

- The **Database tier** requires a DynamoDB database for scalable and reliable data storage.

In combination with these tier-based AWS services and additional services, you have offered the following AWS architecture:

![AWS architecture diagram](/images/2/2/0001.svg?featherlight=false&width=100pc)

After examining of the backbone AWS services of AnimeHub, you group the application infrastructure into the following modules: network, image repository, database, security, routing, app, and web.

{{% notice note %}}
In addition to the core AWS services used in each module, you may declare additional services or make module calls from other modules.
{{% /notice %}}

**Network module**

The network module is in responsible for networking the infrastructure. It has basic VPC components (internet gateway, subnets, etc.). 

![Network module](/images/2/2/0002.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **High privilege:** this module should only be accessible to team members who are authorized to add or remove network resources.

2. **Low volatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

**Image Repository module**

This module is responsible for storing Docker images ECS Fargate pull to run containers.

![Image Repository module](/images/2/2/0003.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **High privilege:** only members of the team that have permission to create or modify resources of image repository should be able to use this module.

2. **Low volatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

**Database module**

The database module sets up and controls the infrastructure required to run the database(s). It now contains the application's DynamoDB table. It may also comprise all related storage, backup data, and logging.

![Database module](/images/2/2/0004.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **High privilege:** only members of the team that have permission to create or modify database resources should be able to use this module.

2. **Low volatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

**Security module**

The security module provides and administers the infrastructure required for any security. It mostly involves IAM resources. It could also include MFA, Certificate Manager, and others.

![Security module](/images/2/2/0005.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **High privilege:** only members of the team that have permission to create or modify IAM / security resources should be able to use this module.

2. **Low volatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

**Routing module**

The routing module builds and manages the infrastructure required for any network routing, including AWS Route 53. 

![Routing module](/images/2/2/0006.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **High privilege:** only members of the team that have permission to create or modify routing resources should be able to use this module.

2. **Low volatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

**App module**

The app module creates and manages the infrastructure needed to run the app tier application. Together with ECS Fargate, Application Load Balancer, and Auto Scaling Group, it also includes VPC Endpoints for connecting to the Image Repository and Database module. This module calls the AWS Certificate Manager, as defined in the Security module, to produce a TSL certificate.

![App module](/images/2/2/0007.svg?featherlight=false&width=37pc)

This module contains these resources because they are **highly encapsulated** and have **highly volatility**.

1. **Highly encapsulated:** this module's resources have a narrow scope and are specifically related to the app tier application. To make it easier for members of team to deploy them, they should be combined into a single module.

2. **Highly volatility:** with every release of the code, this module's resources are updated often. You reduce risk to other modules and needless churn by isolating them into their own module.
   

**Web module**

The web module consists of components that host AnimeHub's client side and allow better content delivery to audiences around the world. In addition, this module implements the call to the AWS Certificate Manager defined in the Security module to provide a TSL certificate.

![Web module](/images/2/2/0008.svg?featherlight=false&width=37pc)

This module contains these resources because they are **highly encapsulated** and have **highly volatility**.

1. **Highly encapsulated:** this module’s resources have a narrow scope and are specifically related to the web tier application. To make it easier for members of team to deploy them, they should be combined into a single module.


2. **Highly volatility:** with every release of the code, this module's resources are updated often. You reduce risk to other modules and needless churn by isolating them into their own module.

#### Graph of Dependencies between Modules

The module-based approach introduces a good amount of isolation between components, minimizing damage to only a small part of the whole infrastructure.

However, that advantage comes with drawbacks:

**Working with multiple folders**

Splitting components into different folders prevents you from accidentally destroy your entire infrastructure in one command, but it also prevents you from creating your entire infrastructure in one command.

**Resource dependencies**

Terraform automatically explore dependencies between resources placed in a configuration. Creating several modules complicates resource dependencies.

{{% notice tip %}}
If you still prefer to use Terraform modules while simultaneously avoiding such problems, you should consider using third-party tools or developing your own.
{{% /notice %}}

You can call modules in a single *main.tf* configuration that is located in a root module, as shown below. Terraform will detect dependencies between modules if necessary, and you can execute all modules in a single run. However, you may again fall into a single-point-of-failure situation, potentially destroying your infrastructure all at once.

```hcl
# root-module/main.tf

module "network" {
    source = "path/to/network-module"

    # Other inputs
    # ...
}

module "web" {
    source = "path/to/web-module"
    
    # Other inputs
    # ...
}

# Other child module calls
# ...

```

You, otherwise, have to consider the risk of causing significant infrastructure damage against the convenience of automation with just one click. The AnimeHub team does not want to take the risk of shutting their platform down with a simple backbone update. You are going to assist them in identifying dependencies between modules and determining the appropriate order to provision the AnimeHub infrastructure.

The graph below shows the inputs and outputs that describe the relationships between modules while ignoring module-specific inputs or outputs that do not indicate the connections between them. Additionally, the graph includes module execution order numbered from **1** to **7**:

![AWS module dependencies](/images/2/2/0009.svg?featherlight=false&width=50pc)

