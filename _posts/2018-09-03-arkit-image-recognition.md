---
layout: post
title: 「译」ARKit 教程：实现 2D 图片识别
date: 2018-09-03 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-image-recognition/)    -    原文日期：2018-04-18


欢迎来到我们 ARKit 教程系列的第六部分。这周我们将讨论在增强现实中实现图片识别。从 iOS 11.3 开始， ARKit 已经有能力识别 2D 图像了。如果你对学习关于构建一个使用 ARKit 识别 2D 图像的 App 感兴趣的话，那么这篇教程就是为你而写的。

> 通过使用用户环境的已知特征来触发虚拟内容的外观，可以增强许多 AR 体验。例如，一个博物馆 app ，当用户用设备对着一副画的时候，可以展示一个虚拟馆长，或者对于一个棋盘游戏，当玩家将设备指向一个棋盘时，可以放一个些虚拟的棋子。在 iOS 11.3 和 之后版本，你可以添加这样的功能到你的 AR 体验中，通过启用 ARKit 的图片识别：你的 App 提供识别 2D 图像， ARKit 会在 AR 会话过程中告诉你这些图像在哪里被识别。
> 
> —— Apple 文档 


### 前提条件

这篇教程需要你对[**我们上一篇讨论的 ARKit 教程里的话题**](https://emptywalker.github.io/2018/08/arkit-light-estimation/)有个深入的理解。如果你是一个 ARKit 新手，请在这里查看[**我们的 ARKit 教程系列**](https://www.appcoda.com/tag/arkit/)。

为了跟着这篇教程，你将需要一个 9.3 或更高的 Xcode 和跑着 iOS 11.3 或更高版本的 Apple 设备。

不多说了，我们开始。

### 你将构建的效果

我们将构建一个 ARKit 图片识别 App 。在任何时候，App 检测到一个可识别的图像，它将会运行一个动画去在真实世界中展示检测到的图片的位置和大小。在动画的上面， App 将会有一个 label ，显示检测到的图片的名字。如果你不明白我的意思，下面的图片将会让你更好理解。

![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p1.gif)


### 开始

首先，通过在[**这里**](https://raw.githubusercontent.com/appcoda/ARKitImageRecognition/master/StarterProject.zip)下面启动项目来开始。启动项目已经提前构建了 UI 元素和行为方法。这样，我们就可以关注在 ARKit 图片识别核心元素上了。

> **提示：**如果你想学习更多关于如何创建专业 UI 元素，请随意查看 [**Beginner iOS Programming with Swift Book**](https://www.appcoda.com/swift/) 和 看看更高级的 [** Intermediate iOS Programming with Swift Book**](https://www.appcoda.com/intermediate-swift-programming-book/) 。
> 

![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p2.png)

一旦你下载了启动项目，在你的 iOS 设备上编译运行它。你应该被提示去允许在你的 App 中访问相机。点击 OK 运行在你的 App 访问相机。

漂亮。现在，让我们进入 ARKit 图片识别的准备图片阶段。