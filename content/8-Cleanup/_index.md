---
title : "Cleanup"
date : "`r Sys.Date()`"
weight : 8
chapter : false
pre : " <b> 8. </b> "
---

Congratulations! What a lengthy workshop! Finally, you provided the AnimeHub team with the fundamental steps for building Terraform modules to provision your recommended AWS infrastructure that suits their needs. Additionally, you assisted them in becoming familiar with Terraform Cloud, which could enhance collaborative Terraform within the team.

You now clean up the Terraform-provisioned AWS resources. Cleaning up resources with Terraform may be faster and less error-prone than manually deleting. 

{{% notice note %}}
Please destroy the resources in the following order: web, app, routing, security, database, image repository, and network workspace.
{{% /notice %}}

**1.** Navigate to your **dev-web** Terraform Cloud workspace interface. Click **Settings**.

![0001](/images/8/0001.svg?featherlight=false&width=100pc)

**2.** On the left sidebar, click **Destruction and Deletion**.

![0002](/images/8/0002.svg?featherlight=false&width=100pc)

**3.** Click **Queue destroy plan**.

![0003](/images/8/0003.svg?featherlight=false&width=100pc)

**4.** Enter `dev-web` as the workspace name. Click **Queue destroy plan**. Then, you will be directed to a new plan that destroy all of the infrastructure managed by Terraform.

![0004](/images/8/0004.svg?featherlight=false&width=100pc)

**5.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![0005](/images/8/0005.svg?featherlight=false&width=100pc)

**6.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will destroy AWS resources for you. After Terraform has done the destroying process, you may access your AWS account to view the Terraform-provided AWS resources destroyed. 

![0006](/images/8/0006.svg?featherlight=false&width=100pc)


**7.** Do the same for the remaining modules from step **1**. Remember to enter the corresponding workspace name in step **4**.
