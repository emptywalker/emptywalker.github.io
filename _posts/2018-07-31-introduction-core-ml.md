---
layout: post
title: 「译」Core ML 介绍：构建一个简单的图片识别APP
date: 2018-07-31 10:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  [原文地址](https://appcoda.com/coreml-introduction/)



在 WWDC 2017， Apple 发布了很多令人兴奋的框架和 APIs 供开发者使用。在所有的新框架中，最受欢迎的无疑是 [Core ML](https://developer.apple.com/documentation/coreml)。 Core ML 是一个可以利用它将机器学习模型集成到应用程序中的框架。Core ML 最棒的地方在于，你不需要对神经网络和机器学习有有广泛的了解；Core ML 的另一个特性是，只要你能将其转换成 Core ML 模型，你就可以使用预先训练好的数据模型。为了演示目的，我们将使用苹果开发者网站上提供的可用的 Core ML 模型。不多说了，让我们来开始学习 Core ML 。

> **提示**：你将需要 Xcode 9 beta 版去完成以下的教程，同时你需要一个跑了 iOS 11 beta 版的设备去测试这个教程的一些效果。虽然 Xcode 9 beta 版同时支持 Swift 3.2 和 Swift 4.0，但所有的代码都是用 Swift 4.0 编写的。


### Core ML 是什么

> Core ML 可让你把各种各样类型的机器学习模型集成到你的应用中。除了支持 30 层以上的深度学习之外，它还支持支持标准模型，如：树集成， SVMs 和广义线性模型。因为它是建立在 Metal 和 Accelerate 这些底层技术之上，Core ML 可以无缝地利用 CPU 和 GPU 来提供最大的性能和效率。你可以在设备上运行机器学习模型，这样数据就不需要离开设备去分析。
> —— [苹果官方文档关于 Core ML 的介绍](https://developer.apple.com/machine-learning/)

Core ML 是一个全新的机器学习框架，在今年的 WWDC 上和 iOS 11 一起发布。你可以使用 Core ML 将机器学习模型集成到你的应用中去。让我们后退一点，什么是机器学习？简单的说，机器学习是赋予计算机学习能力而不是被显示地编程的应用。一个训练模型是将一个机器学习算法和一组训练数据相结合的结果。
![]({{  site.url  }}/assets/screenshot/introduction-coreml/p1.png)

