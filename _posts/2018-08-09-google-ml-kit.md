---
layout: post
title: 「译」在 iOS 中集成 Google ML Kit 进行人脸检测、人脸识别等等
date: 2018-08-09 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  [原文地址](https://www.appcoda.com/mlkit/)   
> 
>   原文时间：2018-06-15

就像 Apple 为她的开发者社区做了很多事情一样，另一家不遗余力的为她的开发者创造出色工具和服务的公司就是 Google 。最近几年， Google 已经发布和改进了它的多个服务，以便给 iOS 和 Android 开发者更多的功能，比如 Google Cloud 、Firebase 、 TensorFlow 等等。

今年，在 Google I/O 2018 上， Google 向她的开发者们发布了一款叫做 [**ML Kit**](https://developers.google.com/ml-kit/) 的全新工具包。 Google 一直处于人工智能领域的最前沿，并且通过让开发者使用 ML Kit 模型，Google 已经赋能给了开发者。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p1.png)

使用 ML Kit ，你需要很少的代码就能够执行各种机器学习的任务。 CoreML 和 ML Kit 一个最主要的区别就是：使用 CoreML ，你必须添加自己的模型，但使用 ML Kit ，你可以依靠 Google 为你提供的模型或者使用你自己的模型。在本教程中，我们将依靠使用 Google 的模型，因为添加自己的 ML 模型需要 TensorFlow 和 非常了解 TensorFlow 。

> **编辑提示：**随着 WWDC 18 的发布，你现在可以在 Xcode 10 的 Playgrounds 中使用 [**Create ML 去创建你自己 ML 模型**](https://emptywalker.github.io/2018/08/create-ml/)


另一个不同点就是如果你的模型太大了，你可以把你的 ML 模型放到 Firebase 中，然后让你的 app 去调用服务器。在 CoreML 中，你只能在设备中跑机器学习模型。这有一个 ML Kit 的能力列表：

* 扫描条形码
* 脸部检测
* 图片标签
* 文本识别
* 地表识别
* 智能回复（还没发布，但快了）

在本教程中，我将向你展示如何用 Firebase 创建一个项目，使用 Cocoapods 去下载依赖包和集成 ML Kit 到你的 app ！我们开始！


### 创建一个 Firebase 项目
第一步就是去  [**Firebase 控制台**](https://www.appcoda.com/mlkit/console.firebase.google.com)。在这儿你将会被提示去登录你的 Google 账号。登录之后，你会看到一个欢迎页面，就像这样。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p2.png)

点击 *Add Project* 按钮，然后给你的项目命名，我们这个项目就叫 `ML Kit Introduction` 。保留 Project ID 并根据需求去修改 *国家/地区*，然后点击 *Create Project* 按钮，这可能会还一点时间。

> **注意：** 在到达限额之前你只能创建一定数量的 Firebase 项目，谨慎创建你的项目。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p3.png)

当所有的事情都搞定了之后，你会看到这样的界面！

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p4.png)

这是你的项目概览页面，并且你可以从这个控制台中操作各种各样的 Firebase 控件。恭喜！你刚刚创建了你第一个 Firebase 项目！保持这个项目不变，我们将切换到第二个来看看 iOS 项目。在[**这里**](https://github.com/appcoda/ML-Kit-Demo/raw/master/mlkit-starter.zip)下载启动项目.