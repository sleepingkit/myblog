---
title:  "Jekyll X GitHub Setup!"
last_modified_at: 2020-05-04 00:00:00 +0800
---
## Create your first Jekyll GitHub page  
1. Install [Jekyll](https://jekyllrb.com/) and create a `Jekyll` project
2. Create a git repository on your `GitHub` account
3. Go to the project root on your local computer
    ``` shell
    git init
    git add remote origin your_git_repo.git  
    ```
4. Change the `baseurl` and `url` in _config.yml file which is under your project root  
    ``` yml
    baseurl: "/myblog" # the subpath of your site, e.g. /blog  
    url: "https://sleepingkit.github.io" # the base hostname & protocol for your site, e.g. http://example.com  
    ```
5. Push all the things to your repository
6. Go to the setting of your `GitHub` repo
    ![github_setting](https://sleepingkit.github.io/myblog/assets/github_setting.png)
7. Change the source of `GitHub` Pages to master branch
    ![github_pages](https://sleepingkit.github.io/myblog/assets/github_pages.png)
8. Wait for a minute and your first `Jekyll` site is built!  

---

> Attention:  
> * Don't change the theme directly in GitHub setting
> * The date of the post you have created should not be the **future date**, otherwise it will skip your post when built
> * Add image should add your website url to your path, you may check the path of the image above

## Change your Jekyll Github page theme  

I suggest to use [Minimal Mistakes]. The following tutorial is talking about how to change the theme in your Github page but not in **local**. If you want to test in your local computer, you may read the documents of [Minimal Mistakes] 

1. Uncommnet this line in ./Gemfile  
   ``` ruby
   gem "github-pages", group: :jekyll_plugins
   ```
2. Add `gem "jekyll-remote-theme"` to the jekyll_plugins in *./Gemfile*  
   ``` ruby
   group :jekyll_plugins do
    gem "jekyll-feed", "~> 0.12"
    gem "jekyll-remote-theme"
    end
   ```
3. Replace the current theme to remote-theme in *./_config.yml*  
   from:  
   ``` yml
   theme: minima
   ```
   to:  
   ``` yml
   remote_theme: "mmistakes/minimal-mistakes@4.19.2"
   ```
4. Add `jekyll-remote-theme` and `jekyll-include-cache` plugins in *./_config.yml*  s
   ``` yml
   plugins:
     - jekyll-feed
     - jekyll-remote-theme
     - jekyll-include-cache
   ```
5. In your posts `yy-mm-dd-post-title.markdown`, remove the YAML Front Matter `layout: post`  
    ```
    ---
    title:  "Post title"
    ---
    ```
6. Add the followings in *_config.yml*, so that you don't have to add the posts layout in every post, same for the other type of layout.  
   ``` yml
    defaults:
        # _posts
        - scope:
            path: ""
            type: posts
          values:
            layout: single
            read_time: true
            comments: true
            share: true
            related: true
            toc: true
            toc_sticky: true
   ```
7. Push your changes to your Github project and see the changes!

[Minimal Mistakes]: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
