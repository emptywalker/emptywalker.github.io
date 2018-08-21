---
layout: post
title: 「译」ARKit 教程：使用 SceneKit 检测水平面并添加 3D 对象
date: 2018-08-17 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-horizontal-plane/)    -    原文日期：2017-12-11



增强现实有能力以前所未有的方式放大世界。我们与世界互动的方式可能再也不会相同。随着 iPhone X 的发布，世界现在比以往任何时候都更愿意接受 AR 。我们正处于历史上的一个特殊时期，而且刚刚开始出现**巨大的**变化。 AR 的潜力无穷无尽。

### 前提条件

本篇教程是建立在上一篇 ARKit 教程的知识之上的。如果你没有准备好，你可以查看一下上一篇[**教程**](https://emptywalker.github.io/2018/08/arkit-3d-object/)。如果你能为你的应用找到一个平面也会很棒。

### 我们将要学习知识

在本教程中，我们焦点是 ARKit 中的水平面，我们将先创建一个海洋（水平面），然后在上面放一个漂亮的船（ 3D 对象）。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p1.jpg)

或者创建一个有光线的舰队。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p2.jpg)

在这个过程中，你将会学习 ARKit 中的水平面。我的希望是通过学完本教程，当你在 ARKit 项目上工作时，你会感觉到利用水平面更加舒适。




