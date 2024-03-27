---
title : "AnimeHub Deployment"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

The most interesting part here! In this section, you are going to deploy AnimeHub to the AWS infrastructure provisoned with Terraform Cloud.

**1.** Go to your GitHub profile `github.com/<your-github-username>`. Mine is [github.com/tulna07](https://github.com/tulna07), for example.

**2.** Click the dropdown and select **New repository**.

![0001](/images/3/1/0001.svg?featherlight=false&width=100pc)

**3.** Name the repository `workshop-1-tf-web-app`.

![0002](/images/6/0002.svg?featherlight=false&width=100pc)

**4.** Leave the other settings as default. Scroll down to the bottom of the page. Click **Create repository**.

![0003](/images/3/1/0003.svg?featherlight=false&width=100pc)

**5.** You next set up the secrets required for the GitHub Actions to work. Click **Settings** in your newly created empty repository.

![0004](/images/6/0004.svg?featherlight=false&width=100pc)

**6.** In the left sidebar, Click the **Secrets and variables** dropdown and then select **Actions**.

![0005](/images/6/0005.svg?featherlight=false&width=100pc)

**7.** Click **New repository secret**.

![0006](/images/6/0006.svg?featherlight=false&width=100pc)

**8.** Name the secrect `AWS_REGION` and the value is `us-east-1`. Click **Add secrect**.

![0007](/images/6/0007.svg?featherlight=false&width=100pc)

**9.** Do the same for other secrects listed below:

|  Secrect Name |  Value |
|---|---|
| `ECR_REPOSITORY` |  `workshop-1` |
| `ECS_CLUSTER_NAME`  |  `dev-workshop-1` |
|  `ECS_SERVICE_NAME` | `app` |
|  `S3_BUCKET_NAME` | `dev-workshop-1` |
|  `GHA_ROLE_FOR_DEPLOYING_BACKEND` | Enter the value you got from step **20** in the [5.6 App Module](../5-terraform-live-repository/6-app-module) section  |
|  `GHA_ROLE_FOR_DEPLOYING_FRONTEND` | Enter the value you got from step **17** in the [5.7 Web Module](../5-terraform-live-repository/7-web-module) section |

**10.** After completing the previous step, you got the following secrects.

![0008](/images/6/0008.svg?featherlight=false&width=100pc)

**11.** Clone this [repository](https://github.com/Definitely-not-AWS-Workshops/workshop-1-web-app).

```git
git clone https://github.com/Definitely-not-AWS-Workshops/workshop-1-web-app.git
```
 
**12.** Move inside the local repository you have just cloned.

```git
cd workshop-1-web-app
```

**13.** Remove the current *.git* folder.

```git
rm -rf .git
```

**14.** Initialize the local repository and link to the remote repository. Replace *\<your-github-username\>* with your GitHub username.

```git
git init && \
git remote add origin https://github.com/<your-github-username>/workshop-1-web-app.git
```

**15.** Push the first commit to the remote repository.

```git
git add . && \
git commit -m "first commit" && \
git push --set-upstream origin main
```

**16.** Create a git tag with the provided version and message.

```git
git tag -a "v1.0.0" -m "release v1.0.0"
```

**17.** Push the tag to the GitHub repository.

```git
git push --follow-tags
```

**18.** Click **Actions** tab on your GitHub repository.

![0009](/images/6/0009.svg?featherlight=false&width=100pc)

**19.** You will see GitHub Actions trigger two workflows that deploy AnimeHub's frontend and backend. Check out *deploy-frontend.yml* and *deploy-backend.yml* files in *.github/workflows* folder to discover more how AnimeHub deployment workflows work.

![00010](/images/6/00010.svg?featherlight=false&width=100pc)

**20.** Wait for the workflows to complete.

![00011](/images/6/00011.svg?featherlight=false&width=100pc)

**21.** Check out AnimeHub at at your domain name. Mine is [https://animehub.tulna07.site](https://animehub.tulna07.site/), for example. Yes! you are successfully access AnimeHub with **https** and your own domain name.

![00012](/images/6/00012.svg?featherlight=false&width=100pc)

**22.** Let us try to share something with other Anime fans worldwide. Click **Share**.

![00013](/images/6/00013.svg?featherlight=false&width=100pc)

**23.** Fill out the **Anime Name** field as `Naruto Shippuden - Chapter 391`, the **Link** field as `https://www.youtube.com/watch?v=Pf5JDzTT8xE`, and the **Description** field as `The Returns of Uchiha Madara`. Otherwise, post your favorite anime instead. Click **Submit**.

![00014](/images/6/00014.svg?featherlight=false&width=100pc)

**24.** Hurray! Your anime goes out to anime enthusiasts worldwide. You can share the link to your friends. Click **Watch** to enjoy now.

![00015](/images/6/00015.svg?featherlight=false&width=100pc)

![00016](/images/6/00016.svg?featherlight=false&width=100pc)

