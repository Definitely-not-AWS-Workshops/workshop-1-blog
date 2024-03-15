---
title : "Terraform Modules"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

You should divide Terraform configuration into modules to reduce the risk of a single point of failure when placing all configurations into a single file.

Choosing which infrastructure to include is one of the most challenging parts of creating a new Terraform module. In this section, we look at the Terraform team's recommendations for module creation. We then determine our necessary module based on those recommendations and their relationships.

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

We would take the recommendations to identify the Terraform modules needed to make AnimeHub operate at scale.

#### Module Proposal

As mentioned in previous section — [AWS Architecture](../1-AWS-Architecture/) , AnimeHub is divided into three main parts: web, app, and database: 

- The **Web tier** requires an S3 bucket for static website hosting and CloudFront for content delivery.

- The **App tier** requires ECS Fargate for containerized application deployment, an Application Load Balancer for efficient traffic distribution, and an Auto Scaling Group for managing resource allocation dynamically.

- The **Database tier** requires a DynamoDB database for scalable and reliable data storage.

In combination with these tier-based AWS services and additional services, we have offered the following AWS architecture:

![AWS architecture diagram](/images/2/2/0001.svg?featherlight=false&width=100pc)

After an examination of the backbone services of AnimeHub, we group the application infrastructure into the following modules: Network, Security, Web, App, Image Repository, Database.

**Network module**

The network module is in responsible for networking the infrastructure. It has basic VPC components (internet gateway, subnets, etc.) and Route 53 for DNS-related features. 

![Network module](/images/2/2/0002.svg?featherlight=false&width=37pc)

This module contains these resources because they have **high privilege** and **low volatility**.

1. **high privilege:** this module should only be accessible to application team members who are authorized to add or remove network resources.

2. **low valatility:** this module's resources will not be updated very frequently. Placing them in a separate module saves them from risk and needless churn.

Typically, the outputs that the network module returns are used by other modules.

**Image Repository module**

![Image Repository module](/images/2/2/0003.svg?featherlight=false&width=37pc)

**Database module**

![Database module](/images/2/2/0004.svg?featherlight=false&width=37pc)

**Security module**

![Security module](/images/2/2/0005.svg?featherlight=false&width=37pc)

**Routing module**

![Routing module](/images/2/2/0006.svg?featherlight=false&width=37pc)

**App module**

The app module creates and manages the infrastructure needed to run the app tier application. It contains ECS Fargate, Elastic Load Balancing, Auto Scaling Group, CloudWatch, and VPC Endpoints required to connect to Image Repository module.

![App module](/images/2/2/0007.svg?featherlight=false&width=37pc)

This module contains these resources because they are **highly encapsulated** and have **highly volatility**.

1.**highly encapsulated:** This module's resources have a narrow scope and are specifically connected to the app tier application. To make it easier for members of the app tier application team to deploy them, they should be combined into a single module.

1. **highly volatility:** With every release of the code, this module's resources are updated often. You reduce risk to other modules and needless churn by isolating them into their own module.

**Web module**

The web module consists of components that host AnimeHub's client side and allow better content delivery to audiences around the world.

![Web module](/images/2/2/0008.svg?featherlight=false&width=37pc)

This module contains these resources because they are **highly encapsulated** and have **highly volatility**.

1.**highly encapsulated:** This module's resources are specifically grouped and focused on improving static website delivery. To make it easier for the web application team to use them, they have been put together into a single module.


2. **highly volatility:** With every release of the code, this module's resources are updated often. You reduce risk to other modules and needless churn by isolating them into their own module.

#### Graph of Dependencies between Modules

The module-based approach introduces a good amount of isolation between components, minimizing damage to only a small part of the whole infrastructure.

However, that advantage comes with drawbacks:

**Working with multiple folders**

Splitting components into different folders prevents you from accidentally destroy your entire infrastructure in one command, but it also prevents you from creating your entire infrastructure in one command.

**Resource dependencies**

Terraform automatically explore dependencies between resources placed in a configuration. Creating several modules complicates resource dependencies.

{{% notice tip %}}
If you prefer using Terraform modules while simultaneously avoiding such problems, you should consider using third-party tools or developing your own.
{{% /notice %}}

You can call modules in *main.tf* file from a single root module, as shown below. Terraform will detect dependencies between them if necessary, and you can execute all modules in a single run. You may again fall into a single-point-of-failure situation, potentially destroying your infrastructure all at once.

```
root-module
├── main.tf
│
├── network
│   ├── main.tf
│   └── ...
│
└── web
    ├── main.tf
    └── ...
```


```hcl
# root-module/main.tf

module "network" {
    source = "./network"

    # Other inputs
    # ...
}

module "web" {
    source = "./web"
    
    # Other inputs
    # ...
}

# Other child module calls
# ...

```

We, otherwise, have to consider the risk of causing significant infrastructure damage against the convenience of automation with just one click. The AnimeHub team does not want to take the risk of shutting their platform down with a simple backbone update. We will assist in identifying dependencies between modules and determining the appropriate order to provision the AnimeHub infrastructure.

![AWS module dependencies](/images/2/2/00010.svg?featherlight=false&width=37pc)

