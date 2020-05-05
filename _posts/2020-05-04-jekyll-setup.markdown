---
layout: post
title:  "Jekyll X Github Setup!"
date:   2020-05-04 00:00:00 +0800
categories: Jekyll setup 
---
# Jekyll X GitHub Setup
1. Install [Jekyll](https://jekyllrb.com/) and create a `Jekyll` project
2. Create a git repository on your `GitHub` account
3. Go to the project root on your local computer
> $ git init  
> $ git add remote origin `your_git_repo.git`  
4. Remove the `.gitignore` file
5. Push all the things to your repository
6. Go to the setting of your `GitHub` repo
![github_setting](https://sleepingkit.github.io/myblog/assets/github_setting.png)
7. Change the source of `GitHub` Pages to master branch
![github_pages](https://sleepingkit.github.io/myblog/assets/github_pages.png)
8. Wait for a minute and your first `Jekyll` site is built!



> Attention:
>
> * Don't change the theme directly in GitHub setting
> * The date of the post you have created on local should not be the future date, otherwise it will skip your post when built
> * Add image should add your website url to your path, you may check the path of the image above
