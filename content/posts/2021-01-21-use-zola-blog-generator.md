+++
title = "使用 Zola 作为静态博客生成器"
+++

# 介绍

官网地址：https://www.getzola.org/

Zola 是一个静态博客生成器，就像 [Hugo](https://gohugo.io/)。

# 与 Hugo 相比

Zola 官方列了一张表格表示自己与Hugo的区别，https://github.com/getzola/zola#comparisons-with-other-static-site-generators 。
从特性上看，Zola 具有更多的特点，但是显然Hugo目前更具知名度。

# 主题

hermit_zola: https://github.com/jessun2017/hermit_zola 做了一些 bugfix。

# Github Page 部署

Zola 官方部署Github静态博客指引：https://www.getzola.org/documentation/deployment/github-pages/

个人推荐的部署方式：

仓库的 src 分支作为Blog撰写分支，将仓库 push 到远程Github。Zola 将 src 分支的静态文件生成后，发布到 master 分支上。
这样就可以直接发布在 <github_name>.github.io 。


```yaml
on:
  push:
    branches: [ src ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 'Checkout'
        uses: actions/checkout@master

      - name: Clean directory ./public/
        run: |
          rm -rf .git/worktrees/public/ 
          rm -fr ./public/

      - name: 'Build and deploy'
        uses: shalzz/zola-deploy-action@master
        env:
          PAGES_BRANCH: master
          BUILD_DIR: .
          TOKEN: ${{ secrets.TOKEN }}
```
