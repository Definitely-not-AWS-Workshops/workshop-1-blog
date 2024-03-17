---
title : "Terraform Cloud General Settings"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.4 </b> "
---

In this section, you are going to set up the Terraform Cloud organization and project. You also add AWS credentials and VCS provider.

To continue with the instructions below, you will need a Terraform Cloud account. Sign in if you have one or create a new account at [app.terraform.io](https://app.terraform.io/).

![0000](/images/3/4/0000.svg?featherlight=false&width=23pc)

**1.** After logging in, navigate to Terrform Cloud's [Getting Started](https://app.terraform.io/app/getting-started) page.

**2.** Choose **Create a new organization**.

![0001](/images/3/4/0001.svg?featherlight=false&width=100pc)

**3.** Choose **Organization name** that is unique. You will use this organization name later in Terraform module configuration. Then, click **Create organization**.

![0002](/images/3/4/0002.svg?featherlight=false&width=100pc)

**4.** After successfully creating the organization, go to **Projects & workspaces** to launch a new project. Otherwise, your workspace will be created under the **Default Project**.

![0003](/images/3/4/0003.svg?featherlight=false&width=100pc)

**5.** Select the **New** dropdown and click **Project**.

![0004](/images/3/4/0004.svg?featherlight=false&width=100pc)

**6.** Name the project `workshop-1` and click **Create**.

![0005](/images/3/4/0005.svg?featherlight=false&width=100pc)

**7.** Click Show or Hide button to see the sidebar.

![0006](/images/3/4/0006.svg?featherlight=false&width=100pc)

**8.** You now see your newly created project named **workshop-1** is empty. Next, you define AWS credentials that allow Terraform to communicate with resources in your AWS account.

![0007](/images/3/4/0007.svg?featherlight=false&width=100pc)

**9.** Navigate to your organization's **Settings**.

![0008](/images/3/4/0008.svg?featherlight=false&width=100pc)

**10.** Click **Variable sets** on the sidebar and then click **Create variable set** to create a *variable set* for your project.

{{% notice info %}}
A *variable set* allows you to create both input variables and environmental variables. Input variables specify the values for variables in your Terraform configuration, whereas environment variables often hold AWS credentials.
{{% /notice %}}

![0009](/images/3/4/0009.svg?featherlight=false&width=100pc)

**11.** Name variable set `workshop-1` and optionally give a description `Variable set for the workshop-1 project`.

![00010](/images/3/4/00010.svg?featherlight=false&width=100pc)

**12.** Scroll down to **Variable set scope**. Choose **Apply to specific projects and workspaces**. Click the **Select projects** dropdown and then select the **workshop-1** project.

![00011](/images/3/4/00011.svg?featherlight=false&width=100pc)

**13.** Scroll down to **Variables**. Click **Add variable**.

![00012](/images/3/4/00012.svg?featherlight=false&width=100pc)

**14.** Select **Environment variable**. Enter **AWS_ACCESS_KEY_ID** as key, and the value is your access key id you obtained in [3.3 AWS Credentials](../3-AWS-Credentials/). Click **Sensitive**, then **Add variable**.

![00013](/images/3/4/00013.svg?featherlight=false&width=100pc)

**15.** Do the same for **AWS_SECRET_ACCESS_KEY** from step **13**. You then got AWS credentials added. Click **Create variable set**.

![00014](/images/3/4/00014.svg?featherlight=false&width=100pc)

**16.** After successfully creating the variable set, you are still in your organization's settings page. You next add the GitHub Version Control System (VCS) provider for our organization. Scroll down the left sidebar and click **Providers**.

![00015](/images/3/4/00015.svg?featherlight=false&width=100pc)

**17.** Click **Add a VCS provider**.

![00016](/images/3/4/00016.svg?featherlight=false&width=100pc)

**18.** Click the **GitHub** dropdown. Select **GitHub.com (Custom)**.

![00017](/images/3/4/00017.svg?featherlight=false&width=100pc)

**19.** Follow step 1 **On GitHub, register a new OAuth Application** to create the required information for the sequential steps.

![00018](/images/3/4/00018.svg?featherlight=false&width=100pc)

**20.** Set the display name for VCS Provider **GitHub.com**. Enter the **Clien ID** and **Client Secrect** values you obtained from the previous step. Click **Connect and continue**.

![00019](/images/3/4/00019.svg?featherlight=false&width=100pc)

**21.** After successfully adding the GitHub VCS Provider, you skip **Advanced settings** page by scrolling down to the bottom and click **Skip and finish**.

![00020](/images/3/4/00020.svg?featherlight=false&width=100pc)








