---
title: "一个奇特的想法 - Site Net"
date: 2021-02-20T11:34:48+08:00
slug: site-net
categories: creation
---

就在最近两天，我思考到关于如何做一个让知识体系更显而易见的个人网站时，想到了一个可以用一个程序来实现展示各网站关联的点子：类似于一个知识体系的构建，知识点 A 关联到知识点 B，知识点 B 关联到知识点 C，每个站点会指向它内部所含的链接的站点。

程序写出来之后首先就想要测试的就是 Wikipedia 的词条，经过域名过滤和标题过滤（让链接仅指向 Wikipedia 的站点而不是某些参考文献），原站点指向的站点实在是太多，图片上的字都有点看不清了。（以下是 Python 词条搜索 3,000 次的结果）

![Wikipedia](assets/wikipedia.png)

然后再试了一下 Apple 的中国官网，图比较密集所以将画布放大了一倍

![Apple](assets/apple.png)

本工程的 [GitHub 链接](https://github.com/cloxnu/SiteNet)

