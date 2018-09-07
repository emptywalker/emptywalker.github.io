---
layout: post
title: 「译」ARKit 教程：保存和恢复 World-mapping 数据来创建持久化的 AR 体验
date: 2018-09-03 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-persistence/)    -    原文日期：2018-08-17


欢迎来到我们 ARKit 教程系列的第八部分。从 iOS 12 开始， ARKit 就有能力持久化 world-mapping 数据。在之前，你是不能保存 world-mapping 数据。 iOS 12 赋予了开发者创建一个持久化 AR 体验的能力。如果你对在增强现实中构建持久化 world-mapping 数据的 Apps 感兴趣的话，那这篇教程就是为你写的。

下面是你将要构建的内容。

![]({{  site.url  }}/assets/screenshot/arkit-persistence/p1.jpg)

就像你从视频上看到的那样，持久化 world-mapping 数据的意思就是你可以保存 world-mapping 数据并在之后又能恢复 world-mapping 数据，即使 App 被终止。这在 iOS 11 中已经成为一个缺陷了。现在，你可以允许用户通过保存 world-mapping 数据来返回之前的 AR 经历。