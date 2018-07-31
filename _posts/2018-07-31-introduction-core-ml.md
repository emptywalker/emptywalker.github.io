---
layout: post
title: 「译」Core ML 介绍：构建一个简单的图片识别 APP 
date: 2018-07-31 10:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
>  [原文地址](https://appcoda.com/coreml-introduction/)



在 WWDC 2017， Apple 发布了很多令人兴奋的框架和 APIs 供开发者使用。在所有的新框架中，最受欢迎的无疑是 [Core ML](https://developer.apple.com/documentation/coreml)。 Core ML 是一个可以利用它将机器学习模型集成到应用程序中的框架。Core ML 最棒的地方在于，你不需要对神经网络和机器学习有有广泛的了解；Core ML 的另一个特性是，只要你能将其转换成 Core ML 模型，你就可以使用预先训练好的数据模型。为了演示目的，我们将使用苹果开发者网站上提供的可用的 Core ML 模型。不多说了，让我们来开始学习 Core ML 。

> **提示**：你将需要 Xcode 9 beta 版去完成以下的教程，同时你需要一个跑了 iOS 11 beta 版的设备去测试这个教程的一些效果。虽然 Xcode 9 beta 版同时支持 Swift 3.2 和 Swift 4.0，但所有的代码都是用 Swift 4.0 编写的。



### Core ML 是什么


> Core ML 可让你把各种各样类型的机器学习模型集成到你的应用中。除了支持 30 层以上的深度学习之外，它还支持支持标准模型，如：树集成， SVMs 和广义线性模型。因为它是建立在 Metal 和 Accelerate 这些底层技术之上，Core ML 可以无缝地利用 CPU 和 GPU 来提供最大的性能和效率。你可以在设备上运行机器学习模型，这样数据就不需要离开设备去分析。
> 
> —— [苹果官方文档关于 Core ML 的介绍](https://developer.apple.com/machine-learning/)

Core ML 是一个全新的机器学习框架，在今年的 WWDC 上和 iOS 11 一起发布。你可以使用 Core ML 将机器学习模型集成到你的应用中去。让我们后退一点，什么是机器学习？简单的说，机器学习是赋予计算机学习能力而不是被显示地编程的应用。一个训练模型是将一个机器学习算法和一组训练数据相结合的结果。
![]({{  site.url  }}/assets/screenshot/introduction-coreml/p1.png)

作为一个应用程序开发者，我们主要关心的是怎样把这个模型应用到我们的程序中来做一些真正有趣的事情。幸运的是，有了 Core ML，Apple 将不同机器学习模型集成到我们的应用中变得非常简单。这位开发者去创建如：图像识别，自然语言处理 ( NLP ) ，文本预测等功能带来了更多可能。

现在你可能会想，将这种人工智能类型的带到你的应用中是否会很困难。这是最好的思考， Core ML 的使用非常简单。在本教程中，你将会看到把 Core ML 集成到我们的应用中只需要花费我们10行代码。

很酷，对吧？让我们开始吧。


### 演示应用概览

我们正在开发的应用很简单，就是让用户可以为物体拍照，也可以从相册中选择一张照片，然后机器学习算法就会预测图片中的物体是什么。结果不一定完美，但你将会了解如果在程序中应用 Core ML 。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p2.png)


### 开始

首先，第一步打开 Xcode 9 并创建一个新项目，为这个项目选择 Single View APP 模板， 确保 language 设置为 Swift。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p3.png)

### 创建用户界面

> **作者提示：** 如果你不想从头开始创建UI，你可以[下载开始项目](https://github.com/appcoda/CoreMLDemo/raw/master/CoreMLDemoStarter.zip)， 然后直接跳到 Core ML 部分。

让我们开始吧，第一件事就是打开 `Main.storyboard`，然后在视图上添加一些UI元素。选中 storyboard 中的 view controller，然后转到 Xcode 的菜单。点击 `Editor-> Embed In-> Navigation Controller`。一旦你完成了这几步，你就会看到一个导航栏出现在你的视图顶部，把导航栏命名为 Core ML （或者其他你认为合适的）。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p4.png)

然后，拖拽两个 bar button items，导航栏标题两边一边一个。对于左边的 bar button item ， 转到 `Attributes Inspector` 并把`System Item`修改为「 Camera 」。右边的 bar button item ，命名为「 Library 」。这两个按钮可以让用户从他/她的相册选一张相片，或者用相机拍一张。

最后你需要添加两个对象，一个 `UILabel` 和一个 `UIImageView` 。把 `UIImageView` 放在视图的中间，把图片视图的宽高修改为 `299x299` 使其成为一个正方形。对于 UILabel ，把它始终放在视图的底部，并拉伸到两边。这就完成了这个APP的UI了。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p5.png)

