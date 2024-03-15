---
title : "What is Infrastructure as Code?"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 1.1 </b> "
---

Infrastructure as code, or IaC, is the concept that you define, deploy, update, and destroy your infrastructure by writing and executing code.

So why use IaC? Why learn a new language and tool and encumber yourself with
yet more code to manage?

The answer is that code is powerful. Moreover, when it comes to application infrastructure maintenance with projects that scale, IaC — and Terraform in particular — can help you save a great deal of time and effort when compared to manual configuration.

![Terraform vs Manual Configuration efficiency graph](/images/1/1/0001.svg?featherlight=false&width=80pc)

You can apply a wide range of software engineering techniques, such as the following, to significantly enhance your software delivery process and manage your project backbone easier when your infrastructure is defined as code:

**Documentation**

If crucial infrastructure information resides solely in the mind of one system administrator, the operation could face setbacks if that person is absent due to vacation, resignation, or unexpected circumstances. IaC resolves this issue by documenting the infrastructure in source files accessible to all. Consequently, even in the absence of the administrator, everyone remains informed about operational procedures, ensuring smooth continuity.

**Version control**

Your infrastructure's entire history is now recorded in the commit log since you can put your IaC source files in version control. This turns into an effective tool for troubleshooting, since whenever an issue arises, the first thing you'll do is look through the commit log to see what modifications were made to your infrastructure. In addition, you may choose to fix the issue by just rolling back to a known-working version of your IaC code.

**Validation**

If your infrastructure is described in code, you can do a code review, run a series of automated tests, and run the code through static analysis tools for each and every update. These procedures are known to greatly lower the likelihood of errors.

**Reuse**

Your infrastructure can be packaged as reusable modules so that you can build upon proven, documented, and tried-and-true components rather than starting from scratch with each deployment for every product in every environment.

