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

另一方面， `style` 文件夹只包含了一张图片： *StarryNight.jpg* 。这个文件夹包含了我们想转换成这样艺术风格的图片。

现在，让我们打开 Jupyter Notebook 开始编码会话，在终端中输入以下代码：

```ssh
jupyter notebook
```
这将会在 Safari 中想下面一样的页面：

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p5.png)

选择 `New` 按钮，然后点击 Python 2 ！

> **注意：**确保你使用的 jupyter notebook 是运行 Python 2 的，因为 Turi Create 不支持 Python 3 。

一旦你点击了那个按钮，一个新的页面就会打开，这就是我们创建模型的地方。点击第一个单元格，然后开始导入 Turi Create 包：

```python
importi  turicreate as tc
```
按下 SHIFT+Enter 键运行这个单元格的代码，等一会儿，直到包被导入。接下来，让我们创建一个包含图片文件夹的引用，请确保你把参数修改成你自己的文件夹路径。

```python
style = tc.load_images('/Path/To/Folder/style')
content = tc.load_images('/Path/To/Folder/content')
```
运行文本区域的代码，你可以收到像这样的输出：

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p6.png)

不用太担心警告。接下来，我们将输入命令去创建风格转换模型。*强烈建议在一台有强大 GPU 的 Mac 上运行下面的代码！这就意味着大多数最新版的 MacBook Pros 和 iMacs 。如果你选择在 MacBook Air 上运行这个代码，比如，计算将会在 CPU 上运行，可能需要运行几天。*

```python
modelm  = tc.style_transfer.create(style, content)
```
运行代码。根据你运行代码的设备，这可能会花费很长的时间才能完成。在我的 MacBook Air 中，花了 3.5 天，因为计算跑在 CPU 上！如果不没有足够的时间，不用担心。你可以在[**这里**]()（ Core ML 模型的名字为「 StarryStyle 」）下载最终的 Core ML 模型。你可以总是让整个函数运行来感受它的样子！

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p7.png)

你看到包含三列的表：*Iteration*、 *Loss* 和 *Elapsed Time* 。在机器学习中，会有一个来回运行多次的函数。当函数向前运行，叫做 **cost** 。当函数向后运行，叫做 **lose** 。函数每跑一次，目标就是调整参数减小 lose 。因此每次参数被改变，就会多添加一次迭代，目标是获得一个更小的 lose 值。当训练的时候，你可以看到 lose 会缓慢的减小。 Elapsed Time 指的是经过了多长时间。

当模型完成训练后，剩下的事就是保存它！这只需要一行代码就能完成！

```python
modelm .export_coreml("StarryStyle.mlmodel")
```

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p8.png)

就这样！去你的库里查看最终的模型！

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p9.png)

### Xcode 项目快览

现在我们有了自己的模型，剩下的就是把它导入到我们的 Xcode 项目。打开 Xcode 9，看一下项目。

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p10.png)

构建和运行项目，确保你可以编译成功。app 还不能立刻工作，当我们按钮 `Van Gogh!` 按钮时，什么也没有发生！这是由编写代码决定的。我们开始！

### 实现机器学习
第一步就是把模型文件（例如 `StarryStyle.mlmodel` ）拖拽到项目中，确保 `Copy Items If Needed` 被选中，和 project target 是选中的。

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p11.png)

接下来，我们得去 `ViewController.swift` 中添加代码去处理机器学习。大部分的代码都会写在我们的 `transformImage()` 方法中。让我们导入 Core ML 包，调用模型吧。

```swift
import CoreML
...
@IBAction func transformImage(_ sender: Any) {
    // 这是 Style Transfer 
    let model = StarryStyle()
}
```

这样简单的一行代码，就把我们的 Core ML 模型分配给了常量 `model`。

#### 转换图片
接下来，我们得去转换用户从可读数据中选择的图片，如果你再看一下 `StarryStyle.mlmodel` 文件，你应该会发现它接受一个大小为 256×256 的图片，因此我们必须转换。在我们的 `transformImage()` 方法正下方，添加一个新的方法。

```swift
func pixelBuffer(from image: UIImage) -> CVPixelBuffer? {
    // 1
    UIGraphicsBeginImageContextWithOptions(CGSize(width: 256, height: 256), true, 2.0)
    image.draw(in: CGRect(x: 0, y: 0, width: 256, height: 256))
    let newImage = UIGraphicsGetImageFromCurrentImageContext()!
    UIGraphicsEndImageContext()
 
    // 2
    let attrs = [kCVPixelBufferCGImageCompatibilityKey: kCFBooleanTrue, kCVPixelBufferCGBitmapContextCompatibilityKey: kCFBooleanTrue] as CFDictionary
    var pixelBuffer : CVPixelBuffer?
    let status = CVPixelBufferCreate(kCFAllocatorDefault, 256, 256, kCVPixelFormatType_32ARGB, attrs, &pixelBuffer)
    guard (status == kCVReturnSuccess) else {
        return nil
    }
       
    // 3   
    CVPixelBufferLockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
    let pixelData = CVPixelBufferGetBaseAddress(pixelBuffer!)
       
    // 4     
    let rgbColorSpace = CGColorSpaceCreateDeviceRGB()
    let context = CGContext(data: pixelData, width: 256, height: 256, bitsPerComponent: 8, bytesPerRow: CVPixelBufferGetBytesPerRow(pixelBuffer!), space: rgbColorSpace, bitmapInfo: CGImageAlphaInfo.noneSkipFirst.rawValue)
       
    // 5
    context?.translateBy(x: 0, y: 256)
    context?.scaleBy(x: 1.0, y: -1.0)
    
    // 6 
    UIGraphicsPushContext(context!)
    image.draw(in: CGRect(x: 0, y: 0, width: 256, height: 256))
    UIGraphicsPopContext()
    CVPixelBufferUnlockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
        
    return pixelBuffer
}
```
这是一个辅助函数，类似于[**早期 Core ML 教程**](https://emptywalker.github.io/2018/07/introduction-core-ml/)中使用的相同函数。万一你不记得了，不用担心。我们一步步来说明这个函数干了什么。
1. 由于我们的模型只接受规格为 `256 x 256` 的图片，我们将图片转换为正方形。然后，我们把正方形图片分配给另一个变量 `newImage` 。
2. 现在，我们把 `newImage` 转换成一个 `CVPixelBuffer` 。如果你对 `CVPixelBuffer` 不熟悉，它主要就是一个图片缓冲区，用于保存主存储器中的像素。你可以在[**这里**](https://developer.apple.com/documentation/corevideo/cvpixelbuffer-q2e)找到关于 `CVPixelBuffers` 的更多信息。
3. 我们然后获取图片中存在的所有像素，并将它们转换为依赖于设备的 RGB 颜色空间。然后，通过将这些所有的数据创建成一个 `CGContext` ，每当我们需要渲染（或改变）它的一些底层属性时，我们都可以轻松的调用它。这是我们在下面两行代码中通过翻转和缩放图片来做的事情。
4. 最后，我们将图形上下文放到当前上下文中，渲染图片，然后从栈顶中移除上下文。这些步骤都做了之后，我们将返回我们的像素缓冲区。

这是真的高级 `Core Image` 代码，已经超出了本教程的范围。如果你去其中大部分都不理解话也不用担心。主要的就是，这个函数提取了一张图片的数据，把这些数据转换成了 Core ML 很容易读取的像素缓冲区。


#### 将 Style Transfer 应用于图像
现在这里我们有了 Core ML 的辅助函数，回到 `transformImage()` 函数并实现它。在我们声明 `model` 的下面一行，添加下面的代码：

```swift
let styleArray = try? MLMultiArray(shape: [1] as [NSNumber], dataType: .double)
styleArray?[0] = 1.0
```

Turi Create 允许你在一个模型中打包多种「风格」。在本项目中，我们只有一种风格：*星夜*。然而，如果你想添加更多的风格，你可以在 `style` 文件夹中添加更多的图片。我们声明 `styleArray` 为 [**`MLMultiArray`**](https://developer.apple.com/documentation/coreml/mlmultiarray) 类型。这是 Core ML 模型用于输入或输出的一种数组类型，我们只有一个形状和一个数据元素。这就是为什么我们把 `styleArray` 的元素数量设为 1 的原因。

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p12.png)

最后，剩下的就是把模型产生的预测设置到 `imageView` 中。

```swift
if let image = pixelBuffer(from: imageView.image!) {
    do {
        let predictionOutput = try model.prediction(image: image, index: styleArray!)
                
        let ciImage = CIImage(cvPixelBuffer: predictionOutput.stylizedImage)
        let tempContext = CIContext(options: nil)
        let tempImage = tempContext.createCGImage(ciImage, from: CGRect(x: 0, y: 0, width: CVPixelBufferGetWidth(predictionOutput.stylizedImage), height: CVPixelBufferGetHeight(predictionOutput.stylizedImage)))
        imageView.image = UIImage(cgImage: tempImage!)
    } catch let error as NSError {
        print("CoreML Model Error: \(error)")
    }
}
```
这个方法首先会检查 `imageView` 中是否有图片了。在代码块中，定义了一个 `predictionOutput` 去存储模型输出的预测。我们调用模型的 `prediction` 方法，传入我们的图片和风格数组。预测的结果是一个像素缓冲区。然而，我们不能给 `UIImageView` 分配一个像素缓冲区，所以我们想出了一个创造性的方法来做这件事。

首先，我们把 `predictionOutput.stylizedImage` 设置成一个 `CIImage` 类型的图片，然后我们创建一个 `CIContext` 实例变量 `tempContext` 。我们可以根据上下文的内置方法（比如：`createCGImage` ）从 `ciImage` 生成一个 `CGImage` 。最后，我们可以把 `imageView` 设置成 `tempImage` 。就这样，如果发生了错误，我们就根据打印的错误日志温和地处理它。

构建和运行你的项目。从你的相册中选择一张图片，然后测试一下 app 的效果如何！

![]({{  site.url  }}/assets/screenshot/prisma-like-app/p13.jpg)

你可能会注意到模型看起来不太接近*星夜*风格，这是由于各种原因。也许我们需要更多的训练数据？又或者我们需要针对更高（或更低）的迭代次数来训练模型，我超级鼓励你重复上面的步骤去不断地调试，直到你得到一个满意的结果！

### 结语
总结一下本教程！我已经向你介绍了 Turi Create 并创建了一个你自己的 Style Transfer 模型，在 5 年前，一个人是不可能创造这样成绩的。你也学习了如果把这个 Core ML 模型导入到你的 iOS app 中，并使用它完成创造性的目的！

然而， Style Transfer 只是一个开始。像我之前提到的那也，Turi Create 可以使用在各种各样的应用程序中。这是一些非常棒的资源，可供你下一步学习：

* [**Apple’s Gitbook on Turi Create Applications**](https://apple.github.io/turicreate/docs/userguide/applications/)
* [**A Guide to Turi Create – WWDC 2018**](https://developer.apple.com/videos/play/wwdc2018/712/)
* [**Turi Create Repository**](https://github.com/apple/turicreate)

想要完整的项目，你可以在 [**GitHub**](https://github.com/appcoda/CoreMLStyleTransfer) 上下载。如果你有任何评论和反馈，请在下面给我留言，分享你的想法。