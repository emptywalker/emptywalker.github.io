---
layout: post
title: 「译」使用 Core ML, Style Transfer 和 Turi Create 创建一个像 Prisma App
date: 2018-08-07 16:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
>  [原文地址](https://www.appcoda.com/coreml-turi-create/)


如果你从去年一直关注 Apple 的公告，你就知道他们在机器学习领域进行了大量投资。自从他们去年在 WWDC 2017 上介绍了 [**Core ML**](https://developer.apple.com/documentation/coreml) 之后，就有大量利用机器学习能力的 app 如雨后春笋般涌现。

然后，开发者总会面对一个挑战就是如何创建一个模型？幸运的是，Apple 在去年冬天宣布从 GraphLab 收购了 Turi Create 的时候，就解决了我们的问题。 [**Turi Create**](https://github.com/apple/turicreate) 是 Apple 帮助开发者简化创建他们自己模型的工具。使用 Turi Create 你可以创建属于你自己的自定义的机器学习模型。


### 快速介绍 Turi Create
如果你已经学过其它机器学习教程了，你可能会思考。*「 Apple 今年不是介绍了 [**Create ML**](https://emptywalker.github.io/2018/08/create-ml/) 了吗？ Turi Create 相对于 CreateML 有什么优势？」*

Create ML 对于刚开始学习 ML 的人来说是个好工具，它在使用上有严重的限制。使用 Create ML ，你被限制在文本或图片数据，虽然大部分项目都是由这些组成，但对于稍微复杂的 ML 应用程序来说（像 Style Transfer ！），它就没什么用。

只要是 Create ML 创建的 Core ML 模型，你都可以使用 Turi Create 创建相同的 Core ML 模型，并且还能创建更多！因为 Turi Create 比 Core ML 更复杂，它和其它 ML 工具像 [**Keras**](https://keras.io/) 和 [**TensorFlow**](https://www.tensorflow.org/) 高度集成。在我们的关于 [**Create ML 教程中**](https://emptywalker.github.io/2018/08/create-ml/)，你可以看到我们使用 Create ML 能够制作 Core ML 模型的类型。下面是你可以使用 Create 做的算法类型：
* [**推荐系统**](https://github.com/apple/turicreate/blob/master/userguide/recommender/README.md)
* [**图片分类**](https://github.com/apple/turicreate/blob/master/userguide/image_classifier/README.md)
* [**图片相似性**](https://github.com/apple/turicreate/blob/master/userguide/image_similarity/README.md)
* [**对象检测**](https://github.com/apple/turicreate/blob/master/userguide/object_detection/README.md)
* [**活动分类器**](https://github.com/apple/turicreate/blob/master/userguide/activity_classifier/README.md)
* [**文本分类器**](https://github.com/apple/turicreate/blob/master/userguide/text_classifier/README.md)

你可以看到上面这个列表是由分类器和回归器，可以使用 Create ML 或者主要地 Turi Create 完成。这就是为什么 Turi Create 受到更有经验的数据科学家的青睐，因为它提供了在 Create ML 中根本没有的可定制性级别。

### 什么是 Style Transfer?
现在你对 Turi Create 有了较深的理解了，让我们来看一下 Style Transfer 是什么。分风格转换就是以其它图片的风格重新组合图像的技术。这是什么意思呢？看一下下面使用 Prisma 创建的图片：

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p1.jpg)

就像你看到的，上面早餐盘的图片被转换成了漫画书的风格了。Style Transfer 是始于 Gatys 等人，发布的一篇[**论文**](https://arxiv.org/abs/1508.06576)，关于是否有可能使用卷积神经网络将艺术风格从一个图片转移到另一个图片上。

> 卷积神经网络（CNNs）是神经网络的一种类型，在机器学习中常用于图片识别和分类。 CNNs 已经在计算机视觉相关的问题上取得了成功，比如识别人脸、物体等等。这是相当复杂的想法，所以我不会太担心。


### 构建我们自己的 Style Transfer

现在你已经了解了我们将在本教程中研究的工具和概念，现在终于开始了！我们将会使用 Turi Create 去构建我们自己的 Style Transfer 模型，然后把它导入示例 iOS 项目中，看看它是如何工作的！

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p2.jpg)

首先，在[**这里下载启动项目**](https://github.com/appcoda/CoreMLStyleTransfer/raw/master/starter.zip)。在本教程中，我们将会使用 Python 2 ， Jupyter Notebook, 和 Xcode 9 。

> 在写作本教程时，很多软件还处于测试阶段，当你开始的时候注意这点。一定要确保使用 Xcode 9 ，因为 Xcode 10 测试版的 Core ML 还有一些问题。这个项目将使用 Swift 4.1 。


### 训练 Style Transfer 模型
Turi Create 是一个 Python 包，但它没有内置在 macOS 中，让我来看看如何快速安装它。你的 macOS 应该已经安装了 Python 。防止在你的设备上没有安装 `python` 或 `pip` ，你可以在[**这里**](https://emptywalker.github.io/2018/08/guide-of-core-ml-tools/)学习安装程序。

#### 安装 Turi Create 和 Jutpyter
打开终端然后输入下面的命令：

```ssh
pip install turicreate==5.0b2
```
等待一到两分钟安装 Python 包。同时，下载 [**Jupyter Notebook**](http://jupyter.org/index.html) 。 Jupyter Notebook 是很多语言的编辑器，因为丰富、可交互的输出可视化从而被开发者们使用。由于 Turi Create 只支持 Python 2，在终端中输入下面的代码给 Python 2 安装 Jupyter Notebook 。

```ssh
python -m pip install --upgrade pip
python -m pip install jupyter
```
![]({{  site.url  }}/assets/screenshot/prisma-like-app/p3.png)

一旦所有的包都安装好了，就可以开始创建我们的算法了！

#### 用 Turi Create 编码

我们将要创建的风格转化模型是来自于 Vincent van Gogh 的*星夜*。主要的是，我们将创建一个模型可以把任意图片转换成*星夜*的复制品。

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p4.jpg)

首先，下载[**训练数据**](https://github.com/appcoda/CoreMLStyleTransfer/raw/master/trainingdata.zip)，并解压它。一个文件夹叫做 `content` ，另一个叫做 `style` 。如果你打开了 `content` ，你将会看到大概 70 张不同主题的图片。这个文件夹中包含了不同图片，因为我们的算法知道将应用到风格转换里的图片的类型。因为我们想转换所有图片，因此我得有多样的图片。