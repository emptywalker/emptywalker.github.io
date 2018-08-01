---
layout: post
title: 「译」Core ML 工具入门指南：将 Caffe 模型转换为 Core ML 模型 
date: 2018-08-01 10:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
>  [原文地址](https://www.appcoda.com/core-ml-tools-conversion/)

欢迎来到 Core ML 教程系列的第二篇。在本篇教程中你将会学到如何设置 Python 虚拟环境，将会获取一个非 Core ML 格式的数据模型，并将该模型转换成 Core ML 模型，最后，将该模型集成到你的应用中。在继续开始本教程之前极力推荐你去阅读一下[前一篇教程](https://emptywalker.github.io/2018/07/introduction-core-ml/)。

在本项目中，我们将会创建一个类似于下面的花卉识别应用。然后，我们主要聚焦在，向你展示对于一个 iOS 开发者如何将一个已经训练好的模型转换成一个 Core ML 格式。

![]({{  site.url  }}/assets/screenshot/guide-of-core-ml-tools/p1.png)

> **注意：**你将需要在 Xcode 9 中完成下面的教程。同时需要一个跑了 iOS 11 的设备去测试本教程的一些功能。最重要的是所有的代码都是用 Swift 4 和 Python 2.7 编写。


### 开始之前 ...
本教程的目的是帮助你学习如何将各种各样格式的数据模型转换成 Core ML 格式。然后在我们开始之前，我先给你介绍一些机器学习框架的背景。有许多流行的深度学习框架为开发者提供工具，用于设计、构建和训练他们自己的模型。我们将会使用来来自于 Caffe 的模型。[ Caffe ](http://caffe.berkeleyvision.org/)是由 Bekerley 人工智能公司 ( BAIR ) 创建的，它是用于创建机器学习模型最常用的框架之一。

除了 Caffe ，还有很多其它的框架，比如：[**Keras**](https://keras.io/)，[**TensorFlow**](https://www.tensorflow.org/) 和 [**Scikit-learn**](http://scikit-learn.org/) 。这些框架都有各自的优缺点，你可以在[这里](https://www.quora.com/Which-neural-network-framework-is-the-best-Keras-Torch-or-Caffe)了解。

在机器学习中，一切都是从模型就开，就可以做出预测和识别系统。教计算机去从一个包含训练数据的机器学习算法中学习。



