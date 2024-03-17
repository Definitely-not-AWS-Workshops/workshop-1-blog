---
title : "Terraform Modules Repository"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

We are going to initialize the *workshop-1-tf-modules* repository with the following folder structure:

```git
workshop-1-tf-modules/
│
└── modules/
    │
    ├── network/
    │   ├── (files and subfolders related to network module)
    │
    ├── image-repository/
    │   ├── (files and subfolders related to image repository module)
    │
    ├── database/
    │   ├── (files and subfolders related to database module)
    │
    ├── security/
    │   ├── (files and subfolders related to security module)
    │
    ├── routing/
    │   ├── (files and subfolders related to routing module)
    │
    ├── app/
    │   ├── (files and subfolders related to app module)
    │
    └── web/
        ├── (files and subfolders related to web module)

```

**1.** Go to your GitHub profile `github.com/<username>`. Remember to replace your GitHub username with *\<username\>*. Mine is [github.com/tulna07](https://github.com/tulna07), for example.

**2.** Click the dropdown and select **New repository**.

![0001](/images/3/1/0001.svg?featherlight=false&width=100pc)

**3.** Name the repository `workshop-1-tf-modules`.

![0002](/images/3/1/0002.svg?featherlight=false&width=100pc)

**4.** Leave the other settings as default. Scroll down to the bottom of the page. Click **Create repository**.

![0003](/images/3/1/0003.svg?featherlight=false&width=100pc)

**5.** Move to your favorite local working directory.

```git
cd <your-working-directory>
```

**6.** Run the following commands to create *workshop-1-tf-modules* folder and the required folder structure one at a time with copy and paste.

```git
repo_name="workshop-1-tf-modules" && \
mkdir "$repo_name" && \
module_names=("network" "image-repository" "database" "security" "routing" "app" "web") && \
for name in "${module_names[@]}"; do \
    mkdir -p "$repo_name/modules/$name"; \
done
```

**7.** Move to the *workshop-1-tf-modules* folder.

```git
cd workshop-1-tf-modules
```

**8.** Create *.gitkeep* as a placeholder for the repository.

```git
touch .gitkeep
```

**9.** Initialize the local repository and link to the remote repository.

```git
git init && \
git remote add origin https://github.com/tulna07/workshop-1-tf-modules.git
```

**10.** Push the first commit to the remote repository.

```git
git add . && \
git commit -m "first commit" && \
git push --set-upstream origin main
```

**11.** The GitHub repository after the first commit. We would later push Terraform module implementations from the local repository and remove *.gitkeep*. 

![0004](/images/3/1/0004.svg?featherlight=false&width=100pc)


