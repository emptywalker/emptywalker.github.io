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

在机器学习中，实现预测和识别系统都是从模型开始的。教计算机去从一个包含训练数据的机器学习算法中学习，训练产生的输出通常称为机器学习模型。解决同样问题（如：物体识别）的机器学习模型有不同的类型，但使用了不同的算法。[**神经网络**](https://en.wikipedia.org/wiki/Artificial_neural_network)、[ **Tree Ensembles** ](https://en.wikipedia.org/wiki/Ensemble_learning)、[ **SVMs** ](https://en.wikipedia.org/wiki/Support_vector_machine)就是这些机器算法中的一部分。

> **作者提示:** 如果你想了解更多的关于机器学习模型知识，你可以看一个[这个](https://medium.com/safegraph/a-non-technical-introduction-to-machine-learning-b49fce202ae8)和[这篇文章](https://dzone.com/articles/introduction-6-machine)。

在 Core ML 刚发布的时候，不支持将所有其它框架模型转换成 Core ML 模型。下面这张图由苹果提供，展示了 Core ML 支持的模型和第三方工具。

![]({{  site.url  }}/assets/screenshot/guide-of-core-ml-tools/p2.png)

我们使用**Core ML 工具**将其他数据模型转换成 Core ML 格式，我们将使用 Python 去下载这些工具并使用它们完成转换。

### 安装 Python 并设置环境

> 许多研究人员和工程师已经用各种结构和数据为不同任务制作了 Caffe 模型。这些模型被学习并应用到从简单的回归、到大规模视觉分类、图片相似性的 Siamese 网络、语言和机器人应用中。
> 
>  -- Caffe Model Zoo

你可以在 GitHub 中发现不同的已经被训练好的 Caffe 模型。为了有效的共享模型， BAIR 引入了[模型动物园框架](http://caffe.berkeleyvision.org/model_zoo.html)，你可以在[这里](https://github.com/BVLC/caffe/wiki/Model-Zoo)找到一些可用的模型。在本文中，我使用[这个 Caffe 模型](https://gist.github.com/jimgoo/0179e52305ca768a601f)向你展示如何将它转换成 Core ML 格式，以及去实现花卉识别。

首先，在这里下载[启动项目](https://www.dropbox.com/s/gtabr24p8iit22y/CoreMLDemoStarter.zip?dl=0)。如果你打开项目看一下代码，你就会发现需要访问相机和相册的代码以及被写好了。你可能回认出这就是从上一篇文章来的，只是缺少了 Core ML 模型。

你应该也注意到了在项目 bundle 中多出来的三个文件： `oxford102.caffemodel` ， `deploy.prototxt` 和 `class_labels.txt` 。这就是 Caffe 模型和将要在 demo 中用到的文件。在后面会做详细介绍。

为了使用 Core ML 工具，第一步就是在你的 Mac 中安装 Python。首先，[下载 Anaconda ](https://www.continuum.io/downloads)（选择 Python 2.7 版本）。 Anaconda 是一种超简单的方式，在你的 Mac 中跑 Python 而不会造成任何问题。一旦你的 Anaconda 安装好了，打开终端，输入以下指令：


```
conda install python=2.7.13
conda update python
```
在这两行代码中，我们安装了我们想要的 Python 版本。在编写本文的时候， Python 2 的最新版是 2.7.13 。以防万一，一旦安装了 Python，输入第二行便可以升级到最新版的 Python 。

下一步就是创建一个虚拟环境，在虚拟环境中，你可以使用不同版本的 Python 或包( packages )去编程。输入下面一行指令去创建一个新的虚拟环境。


```
conda create --name flowerrec
```
当终端给你以下提示时：

```
proceed ([y]/n)?
```
输入「 y 」表示 yes 。恭喜！你现在已经有一个叫做 `flowerrec` 名字的虚拟环境了。

最后，输入下面的命令去安装 Core ML 工具：

```
pip install -U coremltools
```

### 转换 Caffe 模型

再次打开终端，输入下面的指令，进入虚拟环境：


```
source activate flowerrec
```
然后就切换到启动项目的目录下，就是包含了 `class_labels.txt` 、 `deploy.prototxt` 和 `oxford102.caffemodel` 这三个文件的项目。

```
cd <directory>
```

一旦你进入文件夹后，就可以初始化 python 了。只要输入 `python` 就可以在终端中打开一个 Python 界面。第一步就是导入 Core ML 工具，这就是我们要做的事情。

```
import  coremltools
```
下面一行非常重要，请特别注意。输入下面一行代码但不要按回车键。

```python
coreml_model = coremltools.converters.caffe.convert(('oxford102.caffemodel', 'deploy.prototxt'), image_input_names='data', class_labels='class_labels.txt')
```
现在只是很短的一行，这里还有很多。让我们解释一下这三个文件是什么。

1. `deploy.prototxt` - 描述神经网络结构
2. `oxford102.caffemodel` - 用 Caffe 格式训练的数据模型
3. `class_labels.txt` - 包含了模型可能识别出的所有花卉的列表

在上面的语句中，我们定义了一个模型名为 `coreml_model` 接受`coremltools.converters.caffe.convert` 函数的结果，该函数作为 Caffe 到 Core ML 的转换器。这一行最后两个参数是：
1. `image_input_names='data'`
2. `class_labels='class_labels.txt'`

这两个参数定义了我们想 Core ML 接受的输入和输出。这么说吧：计算机只能理解数字，因此，如果我们不添加这两个参数，我们的 Core ML 模型只能接受数字作为输入和输出，而不是接受一个图片作为输入，字符串作为输出。

现在，你可以按**回车**，然后休息一下。根据你机器的计算能力，转换器需要运行一段时间。当转换完成之后，你将会被一个简单的 `>>>` 欢迎。

![]({{  site.url  }}/assets/screenshot/guide-of-core-ml-tools/p2.png)

现在 Caffe 模型已经被转换好了，你需要把它保存下来。可以输入下面的代码完成保存：

```
coreml_model.save('Flowers.mlmodel')
```
`.mlmodel` 文件将会保存在当前文件/目录下。

![]({{  site.url  }}/assets/screenshot/guide-of-core-ml-tools/p2.png)

