---
layout: post
title: 「译」ARKit 教程：通过发射一个火箭飞船来理解物理学
date: 2018-08-23 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-physics-scenekit/)    -    原文日期：2018-01-10

只要那样做就可以移动？这是真的吗？那就是增强现实。欢迎回来 ARKit 系列教程的第四部分。在本教程中，我们将会学习一个 ARKit 里面的物理学基础。我们会在教程结尾发射一艘火箭，然后就像 7 月 4 号一样庆祝一下，因为我们可以做到！我们开始吧！

首先，让我们从[**下载启动项目**](downloading the starter project here)开始。编译并运行，你应该会收到一个弹框，提示许在 App 内访问相机。

![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p1.png)

点击 OK 。如果你一切顺利的话，你应该可以看到你的相机视图。

提醒一下，这篇教程是建立在[**上一篇教程**](https://emptywalker.github.io/2018/08/arkit-horizontal-plane/)知识之上的。如果你有很多困惑，你可以随时查看 [**ARKit 系列教程**](https://www.appcoda.com/tag/arkit/)，以便可以在需要时帮助到你。


### Physics Body 解释
首先要做的事情就是解释 physics body 。这是基本知识之一，为了让 SceneKit 知道如何在 app 中模拟一个 SceneKit 节点。我们需要附带上一个 `SCNPhysicsBody` ， `SCNPhysicsBody` 是一个给节点添加物理仿真的对象。

SceneKit 在渲染一个画面之前会在场景中使用附带的 physics bodies 给节点执行**物理计算**。这些计算包括重力、摩擦力和与其它 bodies 的碰撞。你也可以施力去推动一个 body 。这些计算之后，它会更新节点的位置和方向，然后渲染画面。

通常，在每个渲染画面之前，都会进行物理计算。

### Physics Body 类型

为了构建一个 physics body ，你首先需要指定一个 **physics body 类型**。一个 physics body 类型决定了一个 physics body 如何与其它 bodies 的交互动力。三个  physics body 类型分别是静态，动态，和运动。

#### 静态

你可能想给 SceneKit 对象使用一个静态类型的 physics body ，比如地板，墙面，和地形。一个静态类型的 physics body 是不会受外力或者碰撞所影响的，并且不能移动。

#### 动态
你可能想给 SceneKit 对象使用一个动态类型的 physics body ，比如飞行的喷着火的龙，Steph Curry 正在投篮，或发射一艘火箭。动态 physics body 类型是一个会受外力和碰撞影响的 physics body 。

#### 运动的
你可能想使用一个运动的 physics body 表示当你创建一个游戏的时候，你可以使用你的手指去推动一个方块。因此，你创建了一个「无形的」方块推进器，可以通过你手指移动来触发。这个「无形的」方块不会被其它的方块影响，然后，「无形的」方块会移动其它方块，当它们接触时。运动的 physics body 是一个不受外力和碰撞影响的 physics body ，但当它移动的时候回早晨其它 bodies 的碰撞影响，可以移动其它 bodies ，但不会被其它 bodies 移动。


