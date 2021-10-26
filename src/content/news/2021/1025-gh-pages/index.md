---
title: 用 GitHub Actions 生成 blog 的 HTML 到 gh-pages 分支
date: 2021-10-25T22:00:09+08:00
slug: gh-pages
categories: news
---

由于 blog 生成 HTML 会自动将所有资源复制到生成的目录中，因此将 output 目录放到新的分支中可以防止本地占用「双倍」空间，而且可以防止需要合并远端变更。

这是我的 GitHub Actions 执行代码：

```yml
# This is a basic workflow to help you get started with Actions

name: build creative.clox.nu

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout main
        uses: actions/checkout@v2

      # Update theme 
      - name: Update theme
        run: git submodule update --init --recursive

      # Setup Hugo
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
        
      # Build blog
      - name: Build
        working-directory: ./src/
        run: hugo
        
      - name: Checkout gh-pages
        run: |
          git fetch
          git checkout gh-pages
          ls | grep -v docs | grep -v CNAME | xargs rm -r
          mv docs/* .
          rm -r docs/
      
      # Commit & Push
      - name: Commit & Push changes
        uses: actions-js/push@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: gh-pages
```

其中，

```shell
ls | grep -v docs | grep -v CNAME | xargs rm -r
```

是指删除不包括 docs 目录和 CNAME 文件的所有非隐藏文件。