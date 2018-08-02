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