---
title : "Terraform Live Repository"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

You are going to initialize the *workshop-1-tf-live* repository with the following folder structure:

```git
workshop-1-tf-live/
│
└── environments/
    │
    └── dev/
        │
        ├── network/
        │   ├── (files and submodules related to network module)
        │
        ├── image-repository/
        │   ├── (files and submodules related to image repository module)
        │
        ├── database/
        │   ├── (files and submodules related to database module)
        │
        ├── security/
        │   ├── (files and submodules related to security module)
        │
        ├── routing/
        │   ├── (files and submodules related to routing module)
        │
        ├── app/
        │   ├── (files and submodules related to app module)
        │
        └── web/
            ├── (files and submodules related to web module)
```

**1.** Go to your GitHub profile `github.com/<you-github-username>`. Mine is [github.com/tulna07](https://github.com/tulna07), for example.

**2.** Click the dropdown and select **New repository**.

![0001](/images/3/1/0001.svg?featherlight=false&width=100pc)

**3.** Name the repository `workshop-1-tf-live`.

![0002](/images/3/2/0002.svg?featherlight=false&width=100pc)

**4.** Leave the other settings as default. Scroll down to the bottom of the page. Click **Create repository**.

![0003](/images/3/1/0003.svg?featherlight=false&width=100pc)

**5.** Move to your favorite local working directory.

```git
cd <your-working-directory>
```

**6.** Run the following commands to create *workshop-1-tf-live* folder and the required folder structure one at a time with copy and paste.

```git
repo_name="workshop-1-tf-live" && \
mkdir "$repo_name" && \
module_names=("network" "image-repository" "database" "security" "routing" "app" "web") && \
for name in "${module_names[@]}"; do \
    mkdir -p "$repo_name/environments/dev/$name"; \
done
```

**7.** Move to the *workshop-1-tf-live* folder.

```git
cd workshop-1-tf-live
```

**8.** Create *.gitkeep* as a placeholder for the repository.

```git
touch .gitkeep
```

Until this step, you have created the *workshop-1-tf-live* local repository with the folder structure:

![0004](/images/3/2/0004.svg?featherlight=false&width=17pc)

**9.** Initialize the local repository and link to the remote repository. Replace *\<your-github-username\>* with your GitHub username.

```git
git init &&\
git remote add origin https://github.com/<your-github-username>/workshop-1-tf-live.git
```

**10.** Push the first commit to the remote repository.

```git
git add . & \
git commit -m "first commit" && \
git push --set-upstream origin main
```

**12.** The GitHub repository after the first commit. You would later push Terraform module implementations from the local repository and remove *.gitkeep*. 

![0005](/images/3/2/0005.svg?featherlight=false&width=100pc)


