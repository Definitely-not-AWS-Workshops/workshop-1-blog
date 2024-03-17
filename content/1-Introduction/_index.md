---
title : "Introduction"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1. </b> "
---

Once upon a time, in the vibrant world of anime enthusiasts, there existed a diverse community of fans scattered across the globe, bound together by their shared love for Japanese animation. This community, like any other, sought a sanctuary — a place where they could immerse themselves in the enchanting realms of anime, discuss their favorite shows, and connect with like-minded individuals.

**AnimeHub**, *a platform that would revolutionize the anime-sharing experience*, is developed to satisfy this growing need. As the developers delved deeper into the project, they recognized the importance of not just building a captivating app, but also ensuring its reliability, scalability, and security. They know that to fulfill the dreams of their community, they need a robust infrastructure and a way to manage it that could support their vision. But none of the people on their team have much AWS experience. You will work with them to develop AnimeHub infrastructure that is reliable as well as scalable. Additionally, you make use of Terraform's powers to help them in more quickly deploying and easily managing AWS resources.

AnimeHub currently allows lovers of anime to share their favorite Japanese-style cartoons and explore those posted by others.

![AnimeHub homepage](/images/1/0001.svg?featherlight=false&width=45pc)

![AnimeHub share page](/images/1/0002.svg?featherlight=false&width=45pc)

The days of tedious on-premise resource maintenance and manual configuration of the AnimeHub backbone will be over thanks to AWS and Terraform, which allow AinimeHub developers to concentrate on what really mattered — creating a memorable anime-sharing experience.

The next three pages provide a quick overview of Infrastructure as Code (IaC), Terraform, and notable features of Terraform (Terraform modules and Terraform Cloud workspaces), which are the main things you use in this workshop. You will mainly focus on  creating nessessary Terraform modules manually and apply Terraform Cloud to helps teams use Terraform together. 

If you feel confident with those concepts, proceed to the [High-Level Design](../2-High-Level-Design/) section to explore the designs of AWS architecture, Terraform modules, and Terraform Cloud workspaces for AnimeHub in depth before engaging in hands-on work.

#### Content

1. [What is Infrastructure as Code?](1-What-is-Infrastructure-as-Code)
2. [What is Terraform?](2-What-is-Terraform)
3. [Notable Features of Terraform](3-Notable-Features-of-Terraform)
<!-- need to remove parenthesis for path in Hugo 0.88.1 for Windows-->
