---
layout: post
title: 「译」Core ML 2 新特性
date: 2018-08-02 16:26:24.000000000 +09:00
---

Core ML 是苹果的机器学习框架。一年以前发布的， Core ML 提供了一种只需要几行代码，就可以将强大和智能的机器学习能力集成到应用中的方法。今年，在 WWDC2018 ，苹果发布了 Core ML 2.0 —— Core ML 的下一版本都是围绕着优化模型大小、提高性能和开发者能够指定他们自己的 Core ML 模型来优化流程。

在本文中，我将向你介绍 Core ML 2.0 的所有新特性，以及如何将其应有到你的机器学习应有程序中！如果你是 Core ML 的新手，我推荐你通过[这篇文章](https://emptywalker.github.io/2018/07/introduction-core-ml/)去熟悉 Core ML 。如果熟悉 Core ML，那我们就开始了。

> **注意：**为了尝试这些技术，你需要在你的电脑上安装 macOS Mojave，也需要安装 *coremltools 测试版*。如果没有，不用担心，在后面我会说明如何下载。

### 快速回顾
在 App Store 有很多可以执行强大任务能力的优秀 app 。比如，你可以找到一个文字理解的 app，或者是根据设备运动就知道你在做什么运动的 app ；更有甚者，有些 app 会根据先前的图片对图片进行过滤。这些 app 都有一个共同点：*他们都是使用机器学习的例子，所有这些都可以用 Core ML 模型创建*。

![]({{  site.url  }}/assets/screenshot/new-in-core-ml-2.0/p1.png)

Core ML 使开发者把机器学习模型集成到他们的 app 变得简单，你可以创建一个理解对话上下文和识别不同语音的 app 。此外，苹果已经使开发者可以通过 [ **Vision** ](https://developer.apple.com/documentation/vision) 和 [ **Natural Language** ](https://developer.apple.com/documentation/naturallanguage)这两个框架进行实时图像分析和自然语言理解。

使用 `VNCoreMLRequest` API 和 `NLModel` API，你可以加大提高 app 的 ML 能力，因为 Vision 和 Natural Language 是建立在 Core ML 之上的！

* [ **Vision Tutorial** ](https://www.appcoda.com/vision-framework-introduction/)
* [ **Natural Language Tutorial** ](https://www.appcoda.com/natural-language-processing-swift/)

今年，苹果主要专注于三个要点，去帮助 Core ML 开发者。
1. 模型大小
2. 模型性能
3. 自定义模型

让我们一起探索这3点吧！

### 模型大小

Core ML 的一个巨大优点就是所有事情都是在设备上完成的。这就确保了用户的隐私安全并且计算可以在任何地方进行。然而，随着更高精度的机器学习模型被使用，它们有着很大的体积。把这些模型导入到你的 app 中会占用用户设备的大量空间。

苹果决定给他们的开发者一个工具，去**量化**他们的 Core ML 模型。量化模型是指使用更紧凑的形式去存储和计算数字的技术。在任何机器学习模型的根的核心，就是一个试图计算数字的机器。如果我们可以减少数字或者以一种占用空间更少的形式去存储它们，我们就可以大幅度的减少模型的大小。这可以减少运行时内存的使用和获得更快的计算。

对于一个机器学习模型有三个主要的部分：
* 模型数量
* 权重数量
* **权重大小**

当我们量化一个模型的时候，会减少**权重大小**！在 iOS 11 中， Core ML 是被存储在一个 32 位的模型中，随着 iOS 12 到来，苹果给我们在 16 位甚至 8位模型中存储模型的能力！这就是本文将会看到的内容！

> 如果你不知道什么是权重，这有一个很好的类比。好比，你打算从你家去超时，第一次，你可以走一条特定的路线；第二次，你会尝试找一条短点的路线去超市，因为你已经知道了去超市的路了；而第三次，你将会采取更短的路线，因为你已经知道了前面的两条路了。随着时间的推移，你每次去市场都会学到更短的路径。这种该走哪条路的知识被称为权重。因此，最精确的路径就是权重最大的路径。

让我们开始实践它，开始编码！

### 权重量化