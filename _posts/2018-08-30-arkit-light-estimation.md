---
layout: post
title: 「译」ARKit 教程：具有环境强度和色温的关照估计
date: 2018-08-30 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-light-estimation/)    -    原文日期：2018-02-18


曾几何时，岩石打滑，发出火花，人类学会了生火。这是我们 ARKit 系列的第五部分。今天，我们将学习如何使用 ARKit 在增强现实中实现光照估计。

光照估计是在 AR 中强化你的图像和真实世界的融合 —— 利用着色算法。当你的应用渲染图像时，你可以使用渲染信息和着色算法将相机捕获到的真实世界的光照条件与场景图像相匹配。

我希望你会享受这篇 ARKit 教程。同时希望，这边教程也可以撞击出一个疯狂的想法，就像火箭一样。


现在，让我们开始。

### 前提条件

这篇 ARKit 教程是建立在[**前一篇 ARKit 教程**](https://emptywalker.github.io/2018/08/arkit-physics-scenekit/)的基础上。如果你有任何困惑，你可以随时查看 [**ARKit 系列教程**](https://www.appcoda.com/tag/arkit/)，以便可以在需要时帮助到你。
