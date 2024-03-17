---
title : "Provisioning DERN stack application infrastructure with Terraform"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---

# Provisioning DERN stack application infrastructure with Terraform

#### Overview
This workshop is a hands-on walk through of provisioning simple DERN (DynamoDB, ExpressJS, ReactJS, and NodeJS) stack application infrastructure in AWS with Terraform as the Infrastructure as Code (IaC) tool. Using Terraform to manage AWS resources allows you to rapidly replicate across environments, keep track of infrastructure changes, and drastically minimize manual errors.

The following AWS architecture will be implemented using Terraform, and Terraform Cloud will serve as a platform for the collaborative infrastructure deployment. You then make use of Github Actions for your software's deployment pipelines.

![AWS architecture diagram](/images/0/0001.svg?featherlight=false&width=100pc)

{{% notice note %}}
The purpose of this workshop is not to create comprehensive CI/CD pipelines for Terraform-based AWS infrastructure deployment. It is desirable to include testing, process reviews, and even more software engineering methodologies to guarantee the reliability and reproducibility of your infrastructure across different environments. CI/CD strategies are also not followed by the web and app source code repository. For the sake of demonstration, you just deploy the repository straight from the *main* branch. You must work harder to ensure that the software and its backbone are deployed in a safe and efficient manner. For proposals on additional enhancements, see [Further Improvements]().
{{% /notice %}}

#### Target Audience
While people in technical roles are the target audience for this beginning level workshop, anyone with an interest in AWS and Terraform will find it useful.

Those participating in this workshop are expected to have the following:
- AWS, GitHub, and Terraform Cloud accounts.
- Familiar with AWS CLI and services.
- Basic understanding of [Terraform](https://www.terraform.io/).
- Basic understanding of [Github Actions](https://docs.github.com/en/actions).
- Basic understanding of [Docker](https://www.docker.com/).
- Basic understanding of shell and git commands.
- Your own domain name.

You would focus more on technical decisions and hands-on steps, this workshop thus would not serve as an exhaustive introduction to AWS and Terraform.

#### Duration
The estimated duration for completing this workshop is **around 3 hours**.

#### Costs
Assume that you own a domain name. The services used in this workshop charge per usage, although most of them have a free tier, so unless you used the free tier for other workloads, it will cost you **under $0.5** to run this workshop. Please look at the different services' pricing pages to see their cost.

{{% notice warning %}}
To avoid incurring charges, move to the [Clean Up]() section after the workshop to clean up the resources provisioned.
{{% /notice %}}

#### Github Repositories ####

Visit the following Github repositories to find the complete workshop source codes:

|  Repository |  Description |
|---|---|
| [workshop&#8209;1&#8209;web&#8209;app](https://github.com/Definitely-not-AWS-Workshops/workshop-1-web-app)  |  This repository contains source codes for both web and app components. You will deploy these components to prvisioned AWS infrastructure|
|  [workshop&#8209;1&#8209;tf&#8209;modules](https://github.com/Definitely-not-AWS-Workshops/workshop-1-tf-modules) |  This repository defines reusable modules. Consider every module to be a *"blueprint"* that describes specific elements of your infrastructure  |
|  [workshop&#8209;1&#8209;tf&#8209;live](https://github.com/Definitely-not-AWS-Workshops/workshop-1-tf-live) | This repository defines the live infrastructure you are running in each environment (stage, prod, mgmt, etc.). Consider this as the *"houses"* you constructed using the *"blueprints"* found in the [workshop-1-tf-modules]() |


#### Main Content

1. [Creating a new AWS Account](1-create-new-aws-account/)
2. [Setting up MFA for the AWS Account root user](2-MFA-Setup-For-AWS-User-(root))
3. [Creating an Administrator Accounts and Groups](3-create-admin-user-and-group/)
4. [Getting support for Account Authentication](4-verify-new-account/)
<!-- need to remove parenthesis for path in Hugo 0.88.1 for Windows-->


We welcome your suggestions or improvements! If you have any feedback while running this workshop, feel free to send us an email to [tu.lna07@gmail.com](mailto:tu.lna07@gmail.com).