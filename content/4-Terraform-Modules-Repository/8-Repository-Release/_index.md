---
title : "Repository Release"
date : "`r Sys.Date()`"
weight : 8
chapter : false
pre : " <b> 4.8 </b> "
---

You now release the *workshop-1-tf-modules* local repository to the remote GitHub repository.

**1.** Create a git tag with the provided version and message.

```git
git tag -a "v1.0.0" -m "release v1.0.0"
```

**2.** Push the tag to the GitHub repository.

```git
git push --follow-tags
```