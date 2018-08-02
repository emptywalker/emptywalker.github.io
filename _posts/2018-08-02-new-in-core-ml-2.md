---
layout: post
title: 「译」Core ML 2 新特性
date: 2018-08-02 16:26:24.000000000 +09:00
---

Core ML 是苹果的机器学习框架。一年以前发布的， Core ML 提供了一种只需要几行代码，就可以将强大和智能的机器学习能力集成到应用中的方法。今年，在 WWDC2018 ，苹果发布了 Core ML 2.0 —— Core ML 的下一版本都是围绕着优化模型大小、提高性能和开发者能够指定他们自己的 Core ML 模型来优化流程。

在本文中，我将向你介绍 Core ML 2.0 的所有新特性，以及如何将其应有到你的机器学习应有程序中！如果你是 Core ML 的新手，我推荐你通过[这篇文章](https://emptywalker.github.io/2018/07/introduction-core-ml/)去熟悉 Core ML 。如果熟悉 Core ML，那我们就开始了。

> **注意：**为了尝试这些技术，你需要在你的电脑上安装 macOS Mojave，也需要安装 *coremltools 测试版*。如果没有，不用担心，在后面我会说明如何下载。

