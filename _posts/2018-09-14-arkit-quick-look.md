---
layout: post
title: 「译」介绍 iOS 12 中的 AR Quick Look 
date: 2018-09-14 11:06:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Sai Kambampati](https://www.appcoda.com/author/saikambampati/)    -    [原文地址](https://www.appcoda.com/arkit-quick-look/)    -    原文日期：2018-07-18

在 WWDC 2018 上， Apple 发布了 ARKit 2.0 ，给增强现实开发带来了一系列全新的 APIs 和功能。其中一个功能就是对他们的 Quick Look APIs 的补充。如果你不熟悉 Quick Look 是什么，它是一个基础框架，允许用户预览整个文件，格式如 PDFs ， 图片等！例如 iOS 中的邮件应用使用 Quick Look 来预览附件。

在你的应用中使用 Quick Look 的一个优点就是你只需要声明你可能会快速浏览的是什么文件。框架会处理显示的 UI 和 UX 使得它很容易集成。在继续之前，我建议你看一下[**这篇**](https://www.appcoda.com/quick-look-framework/)关于使用 Quick Look 框架预览文档的教程。

今年，对于 iOS 12 ， Apple 已经介绍了针对增强现实对象的 Quick Look 。这意味着你可以用 Mail ， Messages 或其它支持这种类型的 Quick Look 的应用程序来分享 `.usdz` （稍后详细介绍）文件。接受器不需要下载额外的 App 就可以打开它并查看对象。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p1.jpg)

在上面的图片里，你可以看到在不需要其它的 App 帮助下一个茶壶在 AR 上被预览。 然而，AR Quick Look 不仅仅局限在 App 中使用，你也可以集成 AR Quick Look 到网站中！在这篇教程中，我将会陪你学习在一个 iOS 应用程序中集成 AR Quick Look ，同时，使用 GitHub Pages 构建一个非常基础的 HTML 网站来学习我们如何在网站中添加 AR Quick Look ！只需要在任何运行 iOS 12 的设备上查看 Apple 的 [**AR 图库**](https://developer.apple.com/arkit/gallery/)就可以了！

> **提示：**对于本篇教程，我们将会使用在写作的时候任然处于测试版本的 Xcode 10 。为了看到真正地 AR Quick Look 效果，你需要将 App 运行在跑着 iOS 12 的设备上。在你的 App 开发从始至终都记住这一点。
> 

### 什么是 USDZ ?

在我们开始编码之前，理解什么是 USDZ 非常重要。和很多文件格式一样， USDZ 也是其中之一。它代表**通用场景描述压缩**( Universal Scene Description Zip )。如果你之前接触过 3D 模型，你可能会对 3D 模型比较熟悉，像 `.OBJ` , `.DEA` 或 `.sketchup` 。 USDZ 是由皮克斯（ Pixar ）和 Apple 合作创建一种文件。

其核心，一个 USDZ 文件只不过是 `.zip` 的归档文件，就是将模型和纹理打包成一个单文件。这就是为什么 USDZ 文件被用于 Quick Look 而不是其它 3D 模型格式。

现在你可能会思考，「我该如何创建一个 USDZ 文件？」好的，它实现的方式就是使用你最喜爱的 3D 模型软件( AutoCAD, Blender, Maya, etc 等等)创建你自己的 3D 模型，并使用 Xcode 命令行工具去把这个文件转换成一个 `.usdz` 文件格式。

让我们尝试把我们自己的模型转换成 USDZ 文件格式！

### 把 3D 模型转换成 USDZ 文件格式
一个模型转换成 USDZ 非常简单，只需要一行代码！我们将转换我创建的一颗蛋的 3D 对象模型。你可以在[**这里**](https://github.com/appcoda/AR-Quick-Look-Demo/raw/master/egg.obj)下载这个模型。

当你下载模型，你
