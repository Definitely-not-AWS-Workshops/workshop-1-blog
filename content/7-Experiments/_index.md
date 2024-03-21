---
title : "Experiments"
date : "`r Sys.Date()`"
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

You successfully deployed AnimeHub to AWS. In this section, we are going to do several interesting experiments.

#### CloudFront Caching

**1.** Move to your *workshop-1-web-app* local repository.

```git
cd path/to/workshop-1-web-app
```

**2.** In *workshop-1-web-app/frontend/src/App.jsx* file. Change **AnimeHub** title to **AntiAnimeHub**.

Change

```jsx
<Link to="/" className="navbar_title">
    <h1>AnimeHub</h1>
</Link>
```

to

```jsx
<Link to="/" className="navbar_title">
    <h1>AntiAnimeHub</h1>
</Link>
```

**3.** Commit and push the commit to the GitHub repository.

```git
git add . && \
git commit -m "change the title" && \
git push
```

**4.** Create a git tag with the provided version and message.

```git
git tag -a "v2.0.0" -m "release v2.0.0"
```

**5.** Push the tag to the GitHub repository.

```git
git push --follow-tags
```

**6.** The GitHub Actions workflows will be triggered.

![0001](/images/7/0001.svg?featherlight=false&width=100pc)

**7.** Wait for the workflows to complete and then check out AnimeHub at your domain name. Mine is [animehub.tulna07.site](https://animehub.tulna07.site/), for example. You can see that the title has not changed because the cache has not expired.

![0002](/images/7/0002.svg?featherlight=false&width=100pc)

**8.** Go to your CloudFront distribution.

![0003](/images/7/0003.svg?featherlight=false&width=100pc)

**9.** Click **Invalidations**.

![0004](/images/7/0004.svg?featherlight=false&width=100pc)

**10.** Click **Create invalidation**.

![0005](/images/7/0005.svg?featherlight=false&width=100pc)

**11.**  Add object paths as `/*`. Click **Create invalidation**.

![0006](/images/7/0006.svg?featherlight=false&width=100pc)

**12.** Wait for invalidation process to complete and again check out AnimeHub at your domain name. Mine is [animehub.tulna07.site](https://animehub.tulna07.site/), for example. You now see the title has changed to **AntiAnimeHub**.

![0007](/images/7/0007.svg?featherlight=false&width=100pc)

#### Create A Pull Request to Terraform Live Repository on GitHub

In section [5 Terraform Live Repository](../5-Terraform-Live-Repository/), you initiated the first Terraform Cloud workspace runs manually via UI. You may now try to create a Pull Request, view plan, and merge back into the main branch to start the run automatically.

**1.** Move to your *workshop-1-tf-live* local repository.

```git
cd path/to/workshop-1-tf-live
```

**2.** Create a new branch named **change-dynamodb-rcu** and switch to that branch.

```git
git checkout -b change-dynamodb-rcu
```

**3.** In *workshop-1-tf-live/environments/dev/database/terraform.tfvars*, change **read_capacity** variable from **2** to **3**.

Change

```git
read_capacity = 2
```

to

```git
read_capacity = 3
```

**3.** Commit and push the commit to the GitHub repository.

```git
git add . && \
git commit -m "change the dynamodb read capacity units" && \
git push --set-upstream origin change-dynamodb-rcu
```

**4.** Go to your GitHub *workshop-1-tf-live* repository. Click **Compare & pull request**.

![0008](/images/7/0008.svg?featherlight=false&width=100pc)

**5.** Click **Create pull request**.

![0009](/images/7/0009.svg?featherlight=false&width=100pc)

**6.** Terraform Cloud will trigger a plan. You can click **Details** to see plan's outputs in Terraform Cloud. If everything is fine, click **Merge pull request**.

![00010](/images/7/00010.svg?featherlight=false&width=100pc)

**7.** Move to your **dev-database** Terraform Cloud workspace interface. You see a new run automatically triggered. Click **See details**.

![00011](/images/7/00011.svg?featherlight=false&width=100pc)

**8.** Wait until the plan is finished. After that, review the plan. If everything is fine, scroll down to the bottom and click **Confirm & apply**.

![00012](/images/7/00012.svg?featherlight=false&width=100pc)

**9.** Optionally, add a comment `Look good to me!`. Click **Confirm plan**, Terraform will run apply and change the DynamoDB table's read capacity units. After Terraform has done the applying process, you may access your AWS account to view the changed read capacity units in the DynamoDB table. 

![00013](/images/7/00013.svg?featherlight=false&width=100pc)

**10.** Your table read capacity units now change to 3.

![00014](/images/7/00014.svg?featherlight=false&width=100pc)