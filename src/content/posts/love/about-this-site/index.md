---
title: "关于本站"
date: 2021-02-15T00:21:45+08:00
---

本站使用 Hugo 搭建而成，theme 主要使用 PaperMod，使用 git submodule 拉取，导致手动增加的 Mathjax 特性无法推送至 GitHub，在此作出说明。

以下添加 Mathjax 特性过程参考 [https://note.qidong.name/2018/03/hugo-mathjax/](https://note.qidong.name/2018/03/hugo-mathjax/)

手动添加文件 `themes/PaperMod/layouts/partials/mathjax.html`

![mathjax](assets/mathjax.png)

在 `partials` 目录下的文件 `extend_head.html` 添加代码

![extend_head](assets/extend_head.png)

即可。

