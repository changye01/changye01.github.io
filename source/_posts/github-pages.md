---
title: Hexo & GitAction自动部署GitPages
tag:
- deploy
- hexo
- git_action
- github_page
category:
- hexo
date: 2022-04-18 18:03:01
---

在本教程中，我们使用[GitHub Actions](https://docs.github.com/en/actions)来部署 GitHub Pages。它适用于公共和私有存储库。如果不想将源文件夹上传到 GitHub，请跳至[单命令部署部分。](https://hexo.io/docs/github-pages#One-command-deployment)
<!-- more -->

# 通过分支部署
1. 创建一个名为`username.github.io`的 repo ，其中 username 是您在 GitHub 上的用户名。如果您已经上传到其他 repo，请重命名 repo。
2. 将 Hexo 文件夹中的文件推送到存储库的默认分支。默认分支通常是**main**，较旧的存储库可能使用**master**分支。
    - 将`main`分支推送到 GitHub：
    $ git push -u 起源主  
-  默认情况下不（也不应该）上传该`public/`文件夹，请确保该`.gitignore`文件包含`public/`行。文件夹结构应该与[这个 repo](https://github.com/hexojs/hexo-starter)大致相似，没有`.gitmodules`文件。
3. 检查您在本地计算机上使用的 Node.js 版本，使用`node --version`. 记下主要版本（例如，`v16.y.z`）
4. `.github/workflows/pages.yml`使用以下内容在您的存储库中创建（替换`16`为您在上一步中记下的 Node.js 的主要版本）
```.github/workflows/pages.yml
name: Pages  
  
on:  
  push:  
    branches:  
      - main  # default branch  
  
jobs:  
  pages:  
    runs-on: ubuntu-latest  
    steps:  
      - uses: actions/checkout@v2  
      - name: Use Node.js 16.x  
        uses: actions/setup-node@v2  
        with:  
          node-version: '16'  
      - name: Cache NPM dependencies  
        uses: actions/cache@v2  
        with:  
          path: node_modules  
          key: ${{ runner.OS }}-npm-cache  
          restore-keys: |  
            ${{ runner.OS }}-npm-cache  
      - name: Install Dependencies  
        run: npm install  
      - name: Build  
        run: npm run build  
      - name: Deploy  
        uses: peaceiris/actions-gh-pages@v3  
        with:  
          github_token: ${{ secrets.GITHUB_TOKEN }}  
          publish_dir: ./public
```
5.  部署完成后，可以在`gh-pages`存储库的分支中找到生成的页面。
6.  在 GitHub 存储库的设置中，导航到**Settings** > **Pages** > **Source**。将分支更改为`gh-pages`并保存。
7.  检查_用户名_.github.io 的网页。

# project page

如果你更喜欢在 GitHub 上有一个项目页面：

1. 导航到 GitHub 上的存储库。转到**设置**选项卡。更改**存储库名称**，以便您的博客在`username.github.io/repository`可用, **存储库**可以是任何名称，例如_blog_或_hexo_。
2. 编辑您的`_config.yml`，将`url:`值更改为`https:// _username_.github.io/_repository`。
3. 提交并推送到默认分支。
4. 部署完成后，可以在`gh-pages`存储库的分支中找到生成的页面。
5. 在 GitHub 存储库的设置中，导航到`Settings> Pages > Source`。将分支更改为`gh-pages`并保存。
6.  检查`_用户名_.github.io/_repository_`的网页。