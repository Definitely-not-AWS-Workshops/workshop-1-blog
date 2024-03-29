---
title : "Further Improvements"
date : "`r Sys.Date()`"
weight : 9
chapter : false
pre : " <b> 9. </b> "
---

Last but not least! You may be interested in the following recommendations for improving the workshop in the future.

#### AWS Services

There are different AWS services that might be helpful for the growth of the AnimeHub platform infrastructure:

+ As AnimeHub serves a large number of anime fans worldwide, [AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/) — a service that improves the availability and performance of your applications with local or global users, might be useful.
+ Serverless services, such as [AWS API Gateway](https://docs.aws.amazon.com/apigateway/) and [AWS Lambda](https://docs.aws.amazon.com/lambda/), provide for on-demand server-side processing and cost reductions while also reducing server management overhead.
+ [AWS WAF](https://docs.aws.amazon.com/waf/) — a web application firewall that lets you monitor and manage web requests that are forwarded to protected AWS resources. For example. it helps protect your CloudFront distributions and origin servers from malicious attacks.
+ Wanna build a DDOS Resilient Architecture for this workshop? Check out [Building DDoS Resilient Architecture (Vietnamese)](https://www.youtube.com/watch?v=ZTFnfPbf6FY).
  

#### Operations and Workflows

In addition to security and cost optimization for AWS services at scale, ensuring infrastructure deployment reliability is particularly challenging. To assist teams in effectively adopting AWS and streamlining software engineering processes the following enhancements to the workshop are recommended for the future:

+ Since the workshop did not focus on implementing tagging, check out [Best Practices for Tagging AWS Resources](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/tagging-best-practices.html) for better managing your resources.
+ Use Terraform Cloud API-driven run workflow for more complex CI/CD practices.
+ Consider to leverage [Terraform AWS modules](https://github.com/terraform-aws-modules) — a collection of Terraform AWS modules supported by the community or other reliable remote modules to speed up the development process.
+ Consider to intergrate with 3rd-party tools to address Terraform's shortcomings.
+ Make playbooks to guide individuals or teams through standardizing procedures or situations that enhance productivity, foster teamwork, and guarantee consistency in workflows for software development and operations.
+ Follow test-driven development (TDD) practices for Terraform modules.
+ Use more operational techniques (rollback strategies, deployment strategies, promoting through environments, etc.) to deliver AWS-based infrastructure that is manageable and reliable.
+ Provide sandbox accounts for testing AWS resources and use an AWS account for each environment.

Your team will be able to deploy more quickly and react to changes more quickly and safely if Terraform and collaboration workflows are applied correctly. Deployments should eventually become normal and uninteresting, which is a really positive thing in the world of operations. Furthermore, if you truly do a good job of managing infrastructure, your team will be able to spend more and more time using it, which will enable you to move even more quickly, as opposed to having to manage it all by hand.
