---
layout:     post
title:      "Knuth-Morris-Pratt(KMP) 算法原理、实现及其优化"
subtitle:   " \"写在研究毫无进展的碎碎念\""
date:       2019-07-04 12:00:00
author:     "小兴安岭"
header-img: "img/kmp-bg.jpg"
catalog: true
tags:
    - 算法
    - KMP
---

> “Always bear in mind that your own resolution to succeed is more important than any other.”


## 前言

时光如梭，2019年即将过去一半，年初立下的flag实现了吗？带着这样的疑问，我想是不是该有一个能让我好好写东西的地方呢？于是我借助[Hux的博客模板](https://github.com/Huxpro/huxpro.github.io)搭建了自己的博客。博客的技术实现[如下](#build)。



<p id = "build"></p>
---

## 正文


[GitHub Pages](https://pages.github.com/) + [Jekyll](http://jekyllrb.com/)

[Hux](https://github.com/Huxpro)在Clean Blog的基础上对加载速度和样式进行了一定的优化，详情可见[Hux的第一篇文档](https://github.com/Huxpro/huxpro.github.io/blob/master/_posts/2014-01-29-hello-2015.markdown)。

我采取的方式是 fork [Hux的博客模板](https://github.com/Huxpro/huxpro.github.io) 后将一些特定的内容修改并删除，这些操作可以在[对模板的更改](https://github.com/xiaoxinganling/xiaoxinganling.github.io/blob/master/log.md)中2019.5.29的log查到。除此之外，[Hux](https://github.com/Huxpro)在[此处](https://github.com/Huxpro/huxpro.github.io/blob/master/README.zh.md)提供了更好的搭建方法，此处不再赘述。

---


## 后记

搭建博客的目的在于写博客，写博客的目的在于记录自己的心路历程以及学习点滴，希望看到这里的你们也能成为自己想成为的人。