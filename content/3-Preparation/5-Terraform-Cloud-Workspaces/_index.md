---
title : "Terraform Cloud Workspaces"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 3.5 </b> "
---

You now set up required Terraform Cloud workspaces for the *workshop-1-tf-live* repository.

**1.** Navigate to **Projects & workspaces** page in your Terraform Cloud organization. Select the **New** dropdown and click **Workspace**.

![0001](/images/3/5/0001.svg?featherlight=false&width=100pc)

**2.** Click the **Select Project** dropdown and then choose the **workshop-1** project.

![0002](/images/3/5/0002.svg?featherlight=false&width=100pc)

**3.** Click **Create**.

![0003](/images/3/5/0003.svg?featherlight=false&width=100pc)

**4.** Click **Version Control Workflow**.

![0004](/images/3/5/0004.svg?featherlight=false&width=100pc)

**5.** Choose **GitHub.com** VCS that you have added in the previous section - [3.4 Terraform Cloud General Settings](../4-terraform-cloud-general-settings/).

![0005](/images/3/5/0005.svg?featherlight=false&width=100pc)

**6.** Select the GitHub **\<your-github-username\>/workshop-1-tf-live** repository.

![0006](/images/3/5/0006.svg?featherlight=false&width=100pc)

**7.** Name workspace `dev-network`. Click **Create**.

![0007](/images/3/5/0007.svg?featherlight=false&width=100pc)

**8.** Click on the link that navigates to your newly created workspace page.

![0008](/images/3/5/0008.svg?featherlight=false&width=100pc)

**9.** Click **Settings**.

![0009](/images/3/5/0009.svg?featherlight=false&width=100pc)

**10.** Click **Version Control**.

![00010](/images/3/5/00010.svg?featherlight=false&width=100pc)

**11.** Name the working directory `environments/dev/network`.

![00011](/images/3/5/00011.svg?featherlight=false&width=100pc)

**12.** Select **Only trigger runs when files in specified paths change**, then **Patterns**. Enter the **Path** as `environments/dev/network/**/*.tf*`. Click **Add pattern**. 

![00012](/images/3/5/00012.svg?featherlight=false&width=100pc)

**13.** Scroll down to the bottom, click **Update VCS settings**.

![00013](/images/3/5/00013.svg?featherlight=false&width=100pc)

**14.** Do the same for image repository, database, security, routing, app, and web modules from step **1**.

Remember to replace the **Workspace Name**, **Terraform Working Directory** and **Path** related to the module in step **7**, **11** and **12**, respectively. For example, with the database module, the **Workspace Name** would be `dev-database`, the **Terraform Working Directory** would be `environments/dev/database` and the **Path** would be `environments/dev/database/**/*.*tf*`.