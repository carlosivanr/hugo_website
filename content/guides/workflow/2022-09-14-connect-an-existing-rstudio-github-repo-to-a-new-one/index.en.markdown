---
title: Connect an existing Rstudio github repo to a new one
author: Carlos Rodriguez
date: '2022-09-14'
slug: connect-an-existing-rstudio-github-repo-to-a-new-one
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: "September 14, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
type: book
---

In the event of having to move one github repository a different repository, say because of a switch to an organization, the following commands should complete the task.

## 1 Disconnect the local repository from GitHub
The first step is disconnect the local repository from GitHub. To accomplish this, navigate to the directory of the git repository. Typically, I will open up an Rstudio project that has git version controlled enabled. Then I will open a new bash terminal. The terminal should open in the same directory as the git repository. Finally, type the following bash command to disconnect the local git repot from the GitHub one.


```bash
git remote remove origin
```


## 2 Create a new GitHub repository
The next step is to create a new GitHub repository. This can be done with new organization on the GitHub website. 

## 3 Connect the local git repository to the new GitHub repository
When the new GitHub repository is created a set of commands are provided. Those commands can be copied and pasted into the bash terminal and should complete the setup.


```bash
git remote add origin https://github.com/{USER}/{REPO}.git 

git branch -M main

git push -u origin main
```





