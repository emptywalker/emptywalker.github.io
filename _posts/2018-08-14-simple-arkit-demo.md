---
layout: post
title: 「译」在 Swift 4 和 Xcode 9 中使用 SceneKit 构建一个简单的 ARKit 应用
date: 2018-08-09 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
> 作者：[Jayven N](https://medium.com/@jayvenn)    /    [原文地址](https://www.appcoda.com/mlkit/)    /    原文日期：2017-09-19
> 

增强现实( Augmented Reality  )已经来了，它以一种宏大的方式到来。还记得 Pokemon Go 吗？好的，那只是增加现实的一种口味。从 iOS 11 开始，Apple 正在把增强现实带向大众。随着 iOS 11 的到来，数以亿计的 iPhone 和 iPad 设备具备增强现实的功能。一夜之间， 就打造成了世界上最大的 [**ARKit**](https://developer.apple.com/arkit/) 平台，是的 —— 一夜之间。如果你对在 iOS 11 上构建增强现实的 Apps 感兴趣的话，那么你是来对了地方了。

### 目标

这篇教程写作的目标是通过使用 SceneKit 构建出一个 ARKit 示例 App ，从而帮助你了解 ARKit 的基本原理。

是时候带你下水了，你会看到构建出一个 ARKit App 的全部过程，并通过你的设备让你和 AR 世界进行交互。

这篇教程的主要想法就是通过构建一个 App 去学习技术和它的 APIs 。通过这个过程，你会明白 ARKit 是如何在真实设备上和你创建的炫酷 3D 对象进行交互的。

在太过奇幻之前，让我们先了解一下非常基础的东西，这也是本教程的目的所在。

### 前提条件

本教程建议你充分了解 iOS 开发的基础知识，这是一篇中级教程。你将需要 Xcode 9 或更高版本。

为了测试你的 ARKit App ，你将需要一个部兼容 Apple ARKit 能力的设备，这些设备必须有 Apple A9 或更高版本的处理器。现在，所有东西你都准备好了，且你也适应。让我们开始研究吧！这里有一些我将要带你做的事情：
* 创建针对 ARKit Apps 的项目
* 设置  ARKit SceneKit 视图
* 连接 ARSCNView 和控制器
* 连接 IBOutlet
* 配置 ARSCNView Session
* 允许使用相机
* 给 ARSCNView 添加一个 3D 模型
* 给 ARSCNView 添加自定义手势
* 从 ARSCNView 上移除对象
* 给 ARSCNView 添加多个对象


