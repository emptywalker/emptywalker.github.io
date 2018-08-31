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


### 你将要实现和学习的内容

在创建本篇教程的 ARKit 光照估计项目后，我们将会做到以下内容：

* 在一个已检测的水平面上放一个球体节点
* 使用一个光照节点演示球体节点
* 测试光照强度和温度属性
* 更新和实现 UI
* 最后，在一个 SceneKit 的场景渲染方法中实现光照估计。


### 开始

首先，[**下载启动项目**](https://github.com/appcoda/ARKitLightEstimationDemo/raw/master/ARKitLightEstimationStarter.zip)。我已经构建了应用的 UI 和创建的按钮的行为方法。


![]({{  site.url  }}/assets/screenshot/arkit-light-estimation/p1.png)

编译运行。你应该被提示是否允许在 App 里面访问相机。点击 OK 允许在你的 App 中访问相机。

![]({{  site.url  }}/assets/screenshot/arkit-light-estimation/p2.png)

### 创建一个球体节点

首先，我们将通过在增强现实中创建一个球开始。在 Xcode 中打开 `ViewController.swift` 文件。在 `ViewController` 类中添加以下代码：

```swift
func getSphereNode(withPosition position: SCNVector3) -> SCNNode {
    let sphere = SCNSphere(radius: 0.1)
    
    let sphereNode = SCNNode(geometry: sphere)
    sphereNode.position = position
    sphereNode.position.y += Float(sphere.radius) + 1
    
    return sphereNode
}
```
`getSphereNode(withPosition:)` 方法做了以下事情：

* 传入一个 position 参数
* 创建一个半径为 0.1 CGFloat 的球形几何体
* 使用我们前面创建的球形几何体创建一个球形节点
* 将球型节点的 position 设置成 position 参数的值
* 给球型节点的 y 位置的值加上球体半径值的值，使用球体在检测到的水平面的正上方





