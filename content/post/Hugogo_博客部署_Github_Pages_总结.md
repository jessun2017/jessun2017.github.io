---
title: "使用 hugo 部署 Github Pages 静态博客总结与实践"
date: "2020-06-14"
draft: false
---

## 准备

1. 了解 hugo 博客，请访问[官方文档](https://www.gohugo.org/)。
2. 了解 Github Pages 静态博客，推荐阅读[阮一峰博客关于 Github Pages 的介绍](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)。
3. 了解 Github Actions，推荐这篇[《GitHub Actions 入门教程》](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)。

## 计划方案

对于 hugo 这种静态博客而言，需要进入仓库的其实是两样东西：一个是博客源码，一个是生成后的静态网站文件(hugo 下默认是
`./public` 目录)，其中博客源码包含博客的文章和主题文件，而静态网站文件需要被 Github Pages 服务识别。

1. 新建一个仓库用户存放 blog。如果名字为<username>.github.io，那么 Github Pages 服务识别的分支是 `master` 分支，所以
   master 分支必须是发布后的静态文件。而如果是其他命名，那么 Github Pages 服务可以识别为 `gh-pages` 分支。

2. `master` 分支为发布后文件，那么需要新建一个分支 `src` 作为博客源码。那么博客的写作与修改、站点的设置都在 `src` 分支上
   进行。一旦完成写作，将 `src` 推送到 Github，Github Actions 自动使用 `hugo` 命令将博客静态文件生成至 `master` 分支，然
   后访问 <username>.github.io 来访问博客。

3. Gihub Actions 的自动化处理步骤为：

```yml
# This is a basic workflow to help you get started with Actions

name: Hugo on Github Pages

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
on:
  push:
    branches: [ src ]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - uses: actions/checkout@v2
      with:
        submodules: true

    # Runs a single command using the runners shell
    - name: Clean directory ./public/
      run: |
        rm -rf .git/worktrees/public/
        rm -fr ./public/

    - name: Setup hugo
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.71.1'

    # use hugo cli build blogs
    - name: Build
      run: |
        hugo -b https://jessun2017.github.io

    # Deploy blog site files to master branch
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.TOKEN }}
        publish_branch: master  # default: gh-pages
```
