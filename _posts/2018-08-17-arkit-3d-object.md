---
layout: post
title: 「译」ARKit 教程：理解和实现 3D 对象
date: 2018-08-17 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-3d-object/)    -    原文日期：2017-11-10



拥有炫酷的视觉元素可以给你的 App 带来独一无二的个性。在本篇教程中，我们将着眼于 3D 对象的创建工具箱、3D 对象的线上资源、 SceneKit 支持的格式，最重要的是，去学习使用 SceneKit 创建一个非常简单的 ARKit app 。

不再废话了，我们制作一些 3D 对象，这将会很有趣！我希望你能够享受本篇教程，感谢分享上一篇 ARKit 教程的每个人。


在我们进行之前，需要注意一下前提条件。我们将会建立在上一篇 ARKit 教程的知识点上，如果你是一个 ARKit 新手，请[**查看入门教程**](https://emptywalker.github.io/2018/08/simple-arkit-demo/)，先学习一下如何使用 SceneKit 创建一个简单的 ARKit App 。

### 你将会学到什么。

好的！我们将会一起经历非常多的事情，以下是你将会在本篇教程中学习到的话题：

* 一个简短的介绍，创建 3D 对象工具箱和资源
* 如何实现一个单节点的 3D 对象
* 如果添加基本光线
* 如何实现一个多节点的 3D 对象

### 如何创建和寻找 3D 对象？

当我们要去渲染一些 3D 对象时，我们首先要面对的就是该如何去创建它们或寻找资源。

#### 3D 对象创建工具箱
![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p1.jpg)

首先，你可以使用 3D 对象的创造工具箱去从头开始创造一个 3D 对象。或者，你也可以用这些工具箱将一些现有的特定文件格式的 3D 对象导出成一个 SceneKit 可以兼容的。稍后我们将会接触更多 SceneKit 支持的格式。如果你计划创建自己的 3D 对象，下面有一些工具你可以看一下：
* [**Blender**](https://www.blender.org/)
* [**SketchUp**](https://www.sketchup.com/)
* [**Autodesk 3ds Max**](https://www.autodesk.com/products/3ds-max/overview)


#### 3D 对象的在线资源
相对于创建自己的 3D 对象，你可能更喜欢为你的 ARKit app 获得按需的 3D 模型，下面是一些你可能会发现有用的在线资源：
* [**SketchFab**](https://sketchfab.com/)
* [**TurboSquid**](https://www.turbosquid.com/)
* [**Blocks**](https://vr.google.com/blocks/)


### SceneKit 支持的格式




