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

**5.** Select the the **GitHub** dropdown and choose **GitHub.com** VCS that you have added in the previous section - [3.4 Terraform Cloud General Settings](../4-Terraform-Cloud-General-Settings/).

![0005](/images/3/5/0005.svg?featherlight=false&width=100pc)

**6.** Select the GitHub **\<your-github-username\>/workshop-1-tf-live** repository.

![0006](/images/3/5/0006.svg?featherlight=false&width=100pc)

**7.** Name workspace `dev-network`. Click **Create**.

![0007](/images/3/5/0007.svg?featherlight=false&width=100pc)

**8.** Navigate to **Projects & workspaces** page in your Terraform Cloud organization. Select **workshop-1**. Choose **dev-network** workspace.

![0008](/images/3/5/0008.svg?featherlight=false&width=100pc)
