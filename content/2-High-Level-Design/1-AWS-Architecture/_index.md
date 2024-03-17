---
title : "AWS Architecture"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

#### AnimeHub Components

AnimeHub uses a *three-tier* architecture, which is a sort of the [*n-tier* software design](https://www.baeldung.com/cs/n-tier-architecture), typically consists of the following:

- **Web tier** with **ReactJS** — the component that the user interacts with directly (such webpages and mobile app user interfaces) is responsible for communicating with the app tier via RESTful API endpoints that are accessible via the Internet.
- **App tier** with **NodeJS** and **ExpressJS**— the code required to translate user actions to application functionality (for example, CRUD database operations and data processing).
- **Database tier** with **DynamoDB** — the data storage that hold the data relevant to the application.

A generic three-tier design of AnimeHub is shown in the following figure:

![Three-tier architecture](/images/2/1/0001.svg?featherlight=false&width=40pc)

Since the AnimeHub team includes talented full-stack developers, they prefer to work with a repository that contains both frontend and backend source code. Check out the [workshop-1-web-app](https://github.com/Definitely-not-AWS-Workshops/workshop-1-web-app) repository to see their work. You will eventually deploy this source code to AWS infrastructure with GitHub Actions workflows to serve global users.

You next explore the requirements of the AnimeHub developers and recommend to them the appropriate AWS services for their global software.

#### Web Tier

Since AnimeHub is a worldwide platform that anime fans use regularly to share and find their favorite series, you need to find a way to delivery its static contents faster and more efficient at client-side. Moreover, one of the main goals of the AnimeHub team is to focus on creating the client-side platform, which is built on ReactJS, rather than maintaining the server that hosts it. A combination of [AWS S3](https://docs.aws.amazon.com/s3/) and [AWS CloudFront](https://docs.aws.amazon.com/cloudfront/) might suite their needs.

**AWS S3** 

![AWS S3](/images/2/1/0002.svg?featherlight=false&height=100px&width=100px)

Allows you to serve static websites — such as single-page applications (ReactJS) — straight from an S3 bucket without needing to set up a web server, thereby eliminating the cost of server administration. AWS S3 also offers industry-leading scalability, data availability, security, and speed, with numerous features available.

**AWS CloudFront**

![AWS CloudFront](/images/2/1/0003.svg?featherlight=false&height=100px&width=100px)

A managed content delivery network (CDN) that speeds up distribution of AnimeHub's contents from S3 bucket to users worldwide. Furthermore, to secure your connection to CloudFront when using alternative domain names, you utilize [AWS Certificate Manager](https://docs.aws.amazon.com/acm/) to provide support for provisioning, administering, and renewing publicly trusted TLS certificates.

{{% notice tip %}}
Consider using [AWS WAF](https://docs.aws.amazon.com/waf/), a web application firewall, to protect your CloudFront distributions and origin servers from malicious attacks.
{{% /notice %}}

#### App Tier

Developers on AnimeHub are passionate about [Docker](https://www.docker.com/) and containerized applications, and as you know, they are unwilling to deal with server management. Also, AnimeHub's server-side may handle massive workloads from anime fans around the world. They, therefore, can integrate [AWS Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) and [AWS Auto Scaling](https://docs.aws.amazon.com/autoscaling/) with the [AWS ECS Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html) service.

Anime fans worldwide enjoy sharing their favorite anime with others. As a result, the platform's list of shared anime is constantly changing. Server-side caching and invalidation approaches for AnimeHub using AWS CloudFront would be challenging. Due to list caching, a fan may not see newly shared anime. As a result, you only use AWS CloudFront in front of the App tier, with the *CachingDisabled* policy, for any request to the */api* path.

**AWS ECS Fargate** 

![AWS ECS Fargate](/images/2/1/0004.svg?featherlight=false&height=100px&width=100px)

AWS ECS Fargate is a technology that may be used with Amazon ECS to run AnimeHub RESTful API application's containers without the need to maintain servers or clusters of Amazon EC2 instances. Fargate eliminates the need to provision, configure, and scale virtual machine clusters to execute containers.

**AWS Application Load Balancer**

![AWS Application Load Balancer](/images/2/1/0005.svg?featherlight=false&height=100px&width=100px)

AWS Application Load Balancer distributes incoming traffic among ECS Fargate tasks as targets in one or more Availability Zones. It monitors the health of its registered targets and directs traffic only to the healthy ones.

**AWS Auto Scaling** 

![AWS Auto Scaling](/images/2/1/0006.svg?featherlight=false&height=100px&width=100px)

AWS Auto Scaling helps you ensure that you have the correct number of ECS Fargate tasks available to handle the load for your application. You create collections of ECS Fargate tasks that run server-side AnimeHub's containers, called Auto Scaling groups. 

{{% notice tip %}}
Currently, your RESTfull API endpoint is exposed to the Internet. APIs deal with sensitive data. Authentication and authorization mechanisms should be implemented to protect the API endpoints by ensuring that only approved users or apps are able to manipulate the data.
{{% /notice %}}

#### Database Tier

Though the AnimeHub team is quite familiar with traditional relational databases such as MySQL and PostgreSQL, they would like to try out a new cloud-native database technology that is scalable, easy to use and manage. You offer them [AWS DynamoDB](https://docs.aws.amazon.com/dynamodb/), a serverless non-relational database that is highly available across multiple Availability Zones in an AWS Region while reducing administration overhead.

**AWS DynamoDB**

![AWS DynamoDB](/images/2/1/0007.svg?featherlight=false&height=100px&width=100px)

AWS DynamoDB allows the AnimeHub team to offload the administrative burdens of running and maintaining a distributed database, eliminating the need to worry about hardware provisioning, setup, and configuration, replication, software patching, or cluster scalability. DynamoDB also provides encryption at rest, which reduces the operational load and complexity associated with protecting sensitive data. Additionally, if the workload of AnimeHub is predictable, you can use *"PROVISIONED MODE"* in DynamoDB to save costs.

#### Other AWS Services

The key AWS services for each tier are combined with other services to provide a complete AWS-based infrastructure for the AnimeHub platform. 
- While there may be encryption in transit between AWS services, AWS ECS Fargate would be placed in two private subnets of an [AWS VPC](https://docs.aws.amazon.com/vpc/) for high availability and would securely connect to other services via [AWS VPC Endpoints](https://docs.aws.amazon.com/whitepapers/latest/aws-privatelink/what-are-vpc-endpoints.html). As a result, the communication between these services is not exposed to the Internet.
- You also use an [AWS ECR](https://docs.aws.amazon.com/ecr/) with private repository settings to reliably store our container images.
- [AWS Identity and Access Management](https://docs.aws.amazon.com/iam/) might provide nessessary permissions for our access to the AWS environment.
- [AWS Route 53](https://docs.aws.amazon.com/route53/), a highly available and scalable Domain Name System (DNS) web service, that allows us to use our registered domain name for other AWS services.

#### AWS Architecture

After gathering and identifying requirements for the team and AnimeHub, the following AWS architecture for AnimeHub is our recommendation:

![AWS architecture diagram](/images/2/1/0008.svg?featherlight=false&width=100pc)

Since AnimeHub developers work on [a repository](https://github.com/Definitely-not-AWS-Workshops/workshop-1-web-app) that contains both frontend and backend source code, they would like to use two separate pipelines for deploying to the AWS infrastructure. For the sake of demonstration, you trigger both pipelines at the same time for deployment while tagging a version for our repository. The AWS architecture diagram with deployment pipelines for AnimeHub thus would be:

![AWS architecture diagram](/images/2/1/0009.svg?featherlight=false&width=100pc)