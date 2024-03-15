---
title : "Further Improvements"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

You may be interested in the following recommendations for improving the workshop in the future.

#### AWS Services

There are different AWS services that might be helpful for the growth of the AnimeHub platform infrastructure:

+ As AnimeHub serves a large number of anime fans worldwide, [AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/) — a service that improves the availability and performance of your applications with local or global users, might be useful.
+ Serverless services, such as [AWS API Gateway](https://docs.aws.amazon.com/apigateway/) and [AWS Lambda](https://docs.aws.amazon.com/lambda/), provide for on-demand server-side processing and cost reductions while also reducing server management overhead.
+ [AWS WAF](https://docs.aws.amazon.com/waf/) — a web application firewall that lets you monitor and manage web requests that are forwarded to protected AWS resources.

#### Operations and Workflows

To assist teams in effectively adopting AWS and streamlining software engineering processes the following enhancements to the workshop are recommended for the future:

+ Use Terraform Cloud API-driven run workflow for more complex CI/CD practices.
+ Consider to leverage [Terraform AWS modules](https://github.com/terraform-aws-modules) — a collection of Terraform AWS modules supported by the community or other reliable remote modules to speed up the development process.
+ Consider to intergrate with 3rd-party tools to address Terraform's shortcomings.
+ Make playbooks to guide individuals or teams through standardizing procedures or situations that enhance productivity, foster teamwork, and guarantee consistency in workflows for software development and operations.
+ Follow test-driven development (TDD) practices for Terraform modules.
+ Use more operational techniques (rollback strategies, deployment strategies, promoting through environments, etc.) to deliver AWS-based infrastructure that is manageable and reliable.
+ Provide sandbox accounts for testing AWS resources and use an AWS account for each environment.

Your team will be able to deploy more quickly and react to changes more quickly if Terraform is used correctly. Deployments should eventually become normal and uninteresting, which is a really positive thing in the world of operations. Furthermore, if you truly do a good job of managing infrastructure, your team will be able to spend more and more time using it, which will enable you to move even more quickly, as opposed to having to manage it all by hand.
