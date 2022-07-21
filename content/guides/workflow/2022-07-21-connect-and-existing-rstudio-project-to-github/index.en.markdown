---
title: Connect and existing RStudio project to GitHub
author: Carlos Rodriguez
date: '2022-07-21'
slug: connect-and-existing-rstudio-project-to-github
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: "July 21, 2022"
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

Another nice feature in RStudio is that it can integrate version control via Git and GitHub. This feature allows users to painlessly contribute their code to an online repository to share or keep as a backup. In this post, I will go through the steps of connecting an existing RStudio project to GitHub. The only requirements are that you have a pre-existing GitHub account and Git installed on your local machine.


# In RStudio...
- Open the project that you would like have associated with a GitHub Repository
- Select Tools from the menu bar, then click on Version control, and then Project Setup
- In the Project Options window, select Git/SVN, and then select Git as the version control system in the drop-down menu
- After selecting Git, a pop-up window will ask if you want to initialize a new git repository. 
- Select Yes, and then select Yest to restart the R Session
- Upon restart, a new Git` tab will be available in the Environment pane
- Under the Git tab, select the check boxes of the files to upload to a GitHub repository
- Select the Commit button, type in a Commit message, select the Commit button, and then Close the new window

# On Github...
- Sign in to your account and navigate to your Repositories page
- Then select the green New button, to create a new repository.
- Type in a new repository name and select public or private, then select the green Create repository button.
- From here, I copy the three lines of code from the section titled "... or push existing repository from the command line"

# Back to RStudio...
- If on a linux machine windows, you should be able to go back to RStudio, and open a new terminal window
- Go to Tools in the menu bar,  select Terminal, and then New Terminal
- Then paste in the three commands from the GitHub page

- On a windows machine, you can install Git Bash and you can set RStudio to open Git Bash terminals instead of Command Prompt
- Got to Tools in the menu bar and select Terminal Options
- In the new window that pops up, select Git Bash from the drop down menu and select Apply and OK
- You can then open a new terminal window and paste in the three commands from the GitHub page

That's all there is to setting up an existing Rproject with a GitHub repository. Now when you are ready to commit changes, select the Git tab of in the Environment pane. From there:
- Select Commit
- Type in a Commit message
- Select Commit
- Select the Push button

