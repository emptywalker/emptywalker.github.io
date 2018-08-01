---
layout: post
title: 「译」Core ML 介绍：构建一个简单的图片识别 APP 
date: 2018-07-31 10:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
>  [原文地址](https://appcoda.com/coreml-introduction/)



在 WWDC 2017， Apple 发布了很多令人兴奋的框架和 APIs 供开发者使用。在所有的新框架中，最受欢迎的无疑是 [Core ML](https://developer.apple.com/documentation/coreml)。 Core ML 是一个可以利用它将机器学习模型集成到应用程序中的框架。Core ML 最棒的地方在于，你不需要对神经网络和机器学习有有广泛的了解；Core ML 的另一个特性是，只要你能将其转换成 Core ML 模型，你就可以使用预先训练好的数据模型。为了演示目的，我们将使用苹果开发者网站上提供的可用的 Core ML 模型。不多说了，让我们来开始学习 Core ML 。

> **提示**：你将需要 Xcode 9 beta 版去完成以下的教程，同时你需要一个跑了 iOS 11 beta 版的设备去测试这个教程的一些效果。虽然 Xcode 9 beta 版同时支持 Swift 3.2 和 Swift 4.0，但所有的代码都是用 Swift 4.0 编写的。



### Core ML 是什么


> Core ML 可让你把各种各样类型的机器学习模型集成到你的应用中。除了支持 30 层以上的深度学习之外，它还支持支持标准模型，如：树集成， SVMs 和广义线性模型。因为它是建立在 Metal 和 Accelerate 这些底层技术之上，Core ML 可以无缝地利用 CPU 和 GPU 来提供最大的性能和效率。你可以在设备上运行机器学习模型，这样数据就不需要离开设备去分析。
> 
> —— [苹果官方文档关于 Core ML 的介绍](https://developer.apple.com/machine-learning/)

Core ML 是一个全新的机器学习框架，在今年的 WWDC 上和 iOS 11 一起发布。你可以使用 Core ML 将机器学习模型集成到你的应用中去。让我们后退一点，什么是机器学习？简单的说，机器学习是赋予计算机学习能力而不是被显示地编程的应用。一个训练模型是将一个机器学习算法和一组训练数据相结合的结果。
![]({{  site.url  }}/assets/screenshot/introduction-coreml/p1.png)

作为一个应用程序开发者，我们主要关心的是怎样把这个模型应用到我们的程序中来做一些真正有趣的事情。幸运的是，有了 Core ML，Apple 将不同机器学习模型集成到我们的应用中变得非常简单。这位开发者去创建如：图像识别，自然语言处理 ( NLP ) ，文本预测等功能带来了更多可能。

现在你可能会想，将这种人工智能类型的带到你的应用中是否会很困难。这是最好的思考， Core ML 的使用非常简单。在本教程中，你将会看到把 Core ML 集成到我们的应用中只需要花费我们10行代码。

很酷，对吧？让我们开始吧。


### 演示应用概览

我们正在开发的应用很简单，就是让用户可以为物体拍照，也可以从相册中选择一张照片，然后机器学习算法就会预测图片中的物体是什么。结果不一定完美，但你将会了解如果在程序中应用 Core ML 。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p2.png)


### 开始

首先，第一步打开 Xcode 9 并创建一个新项目，为这个项目选择 Single View APP 模板， 确保 language 设置为 Swift。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p3.png)

### 创建用户界面

> **作者提示：** 如果你不想从头开始创建UI，你可以[下载开始项目](https://github.com/appcoda/CoreMLDemo/raw/master/CoreMLDemoStarter.zip)， 然后直接跳到 Core ML 部分。

让我们开始吧，第一件事就是打开 `Main.storyboard`，然后在视图上添加一些UI元素。选中 storyboard 中的 view controller，然后转到 Xcode 的菜单。点击 `Editor-> Embed In-> Navigation Controller`。一旦你完成了这几步，你就会看到一个导航栏出现在你的视图顶部，把导航栏命名为 Core ML （或者其他你认为合适的）。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p4.png)

然后，拖拽两个 bar button items，导航栏标题两边一边一个。对于左边的 bar button item ， 转到 `Attributes Inspector` 并把`System Item`修改为「 Camera 」。右边的 bar button item ，命名为「 Library 」。这两个按钮可以让用户从他/她的相册选一张相片，或者用相机拍一张。

最后你需要添加两个对象，一个 `UILabel` 和一个 `UIImageView` 。把 `UIImageView` 放在视图的中间，把图片视图的宽高修改为 `299x299` 使其成为一个正方形。对于 UILabel ，把它始终放在视图的底部，并拉伸到两边。这就完成了这个APP的UI了。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p5.png)


### 实现相机和相册功能

现在我们已经设计好了UI，让我们继续实现。在这部分我们将会实现 library 和 camera 的功能。在 `ViewController.swift` 中，首先采用 `UIImagePickerController` 类所需要的 `UINavigationControllerDelegate` 协议。


```

class ViewController: UIViewController, UINavigationControllerDelegate

```

然后给 label 和 image view 添加两个新的 outlets。为了简单起见，我已经把 `UIImageView` 命名为 imageView ，把 `UILabel` 命名为 classifier 。你的代码看起来应该像这样：


```

import UIKit
 
class ViewController: UIViewController, UINavigationControllerDelegate {
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var classifier: UILabel!
    
     override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
}


```


接下来，你要为来自于 bar button items 的点击事件创建各自的行为。现在在 `ViewController` 类中插入以下操作方法：


```

@IBAction func camera(_ sender: Any) {
    
    if !UIImagePickerController.isSourceTypeAvailable(.camera) {
        return
    }
    
    let cameraPicker = UIImagePickerController()
    cameraPicker.delegate = self
    cameraPicker.sourceType = .camera
    cameraPicker.allowsEditing = false
    
    present(cameraPicker, animated: true)
}
 
@IBAction func openLibrary(_ sender: Any) {
    let picker = UIImagePickerController()
    picker.allowsEditing = false
    picker.delegate = self
    picker.sourceType = .photoLibrary
    present(picker, animated: true)
}


```

总结一下我们在每个行为中做的事情，我们创建了一个 `UIImagePickerController` 的常量，然后确保了用户不可以编辑选择的图片（不论是来自于相册还是相机）。然后我们将代理设置为它自己，最后我们向用户展示了 `UIImagePickerController` 。

因为我们没有将 `UIImagePickerControllerDelegate` 类方法添加到 `ViewController.swift` 中，我们将会收到一个错误。我们将会使用一个扩展去采用代理方法：
 
 
```

extension ViewController: UIImagePickerControllerDelegate {
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        dismiss(animated: true, completion: nil)
    }
}


```

上面的一行代码将会处理用户取消图片选择的操作。他还会将 `UIImagePickerControllerDelegate` 的类方法分配给我们的 Swift 文件（译者注：触发取消图片处理的代理方法）。现在你的代码看来有点像这样。


```

import UIKit
 
class ViewController: UIViewController, UINavigationControllerDelegate {
    
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var classifier: UILabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
 
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    @IBAction func camera(_ sender: Any) {
        
        if !UIImagePickerController.isSourceTypeAvailable(.camera) {
            return
        }
        
        let cameraPicker = UIImagePickerController()
        cameraPicker.delegate = self
        cameraPicker.sourceType = .camera
        cameraPicker.allowsEditing = false
        
        present(cameraPicker, animated: true)
    }
    
    @IBAction func openLibrary(_ sender: Any) {
        let picker = UIImagePickerController()
        picker.allowsEditing = false
        picker.delegate = self
        picker.sourceType = .photoLibrary
        present(picker, animated: true)
    }
 
}
 
extension ViewController: UIImagePickerControllerDelegate {
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        dismiss(animated: true, completion: nil)
    }
}


```

确保你回到 storyboard 中，所有的 outlet 变量 和 操作方法都是连接着的。

为了访问相册和相机，你还得做最后一件事情。进入 `Info.plist` 然后添加两个条目： Privacy – Camera Usage Description 和 Privacy – Photo Library Usage Description 。从 iOS 10 开始，你需要特别说明你的应用需要访问相机和相册的原因。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p6.png)

好的，就这样。你现在准备移到本教程的核心部分。再重复一遍，如果你不想从头创建演示应用，[在这里下载开始项目](https://github.com/appcoda/CoreMLDemo/raw/master/CoreMLDemoStarter.zip)。

### 集成 Core ML 数据模型

现在，我们转换一下话题，集成 Core ML 数据模型到我们的应用中。之前提到过，我们需要一个提前训练好的模型来和 Core ML 一起工作。你可以创建自己的模型，但对于这个实例来说，我们将使用苹果开发者网站上已经训练好的模型。

去苹果的[机器学习](https://developer.apple.com/machine-learning/)开发者网站，滚到页面底部，你将会发现4个提前训练好的模型。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p7.png)

本教程中，我们使用 *Inception v3* 模型，但你可以随意尝试另外三个。一旦就下载了 *Inception v3* 模型，就将它添加到 Xcode 项目中，并查看一下它的显示。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p8.png)

> **注意：** 请确保项目的 Target Membership 被选中，否则，你的应用将不能访问数据模型文件。


在上面的截图中，你可以看到数据模型的类型，即神经网络分类器。另外一个需要注意的就是模型评估参数。它告诉你模型接受的输入和模型返回的输出。这里接受一个 299×299 的图片，返回给你一个最可能的类别，加上每个类别的概率。

你还会注意到模型类。这是由机器学习模型生成的模型类( `Inceptionv3` )，我们可以直接在代码中使用它。如果你点击 `Inceptionv3` 边上的箭头，你将会看到这个类的源码。

![]({{  site.url  }}/assets/screenshot/introduction-coreml/p9.png)

现在，让我们在你的代码中添加模型。回到 `ViewController.swift` 文件中。首先，在开头导入 CoreML 框架：


```

import CoreML

```

然后，在类中为 `Inceptionv3` 模型声明一个 `model` 变量，并且在 `viewWillAppear()` 方法中初始化它。


```

var model: Inceptionv3!
 
override func viewWillAppear(_ animated: Bool) {
    model = Inceptionv3()
}

```

我知道你可能正在思考。

「那么，为什么我们不能早点初始化这个模型呢？」

「把它规定在 `viewWillAppear` 方法中，有什么好处呢？」

好吧，朋友们，关键是当你的应用试图识别你照片中的物体是什么时，它会快很多。

现在，如果我们返回到 `Inceptionv3.mlmodel` ，我们会发现这个模型只接受一个图片规格为 299x299 的输入。因此，怎样把一个图片转成长合格规格呢？这就是我们接下来要解决的问题。


### 图片转换

在 `ViewController.swift` 的扩展中，更新代码如下。我们实现了 `imagePickerController(_:didFinishPickingMediaWithInfo)` 方法去处理选中的图片。


```
extension ViewController: UIImagePickerControllerDelegate {
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        dismiss(animated: true, completion: nil)
    }

    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        picker.dismiss(animated: true)
        classifier.text = "Analyzing Image..."
        guard let image = info["UIImagePickerControllerOriginalImage"] as? UIImage else {
            return
        } 
        
        UIGraphicsBeginImageContextWithOptions(CGSize(width: 299, height: 299), true, 2.0)
        image.draw(in: CGRect(x: 0, y: 0, width: 299, height: 299))
        let newImage = UIGraphicsGetImageFromCurrentImageContext()!
        UIGraphicsEndImageContext()
        
        let attrs = [kCVPixelBufferCGImageCompatibilityKey: kCFBooleanTrue, kCVPixelBufferCGBitmapContextCompatibilityKey: kCFBooleanTrue] as CFDictionary
        var pixelBuffer : CVPixelBuffer?
        let status = CVPixelBufferCreate(kCFAllocatorDefault, Int(newImage.size.width), Int(newImage.size.height), kCVPixelFormatType_32ARGB, attrs, &pixelBuffer)
        guard (status == kCVReturnSuccess) else {
            return
        } 
        
        CVPixelBufferLockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
        let pixelData = CVPixelBufferGetBaseAddress(pixelBuffer!)
        
        let rgbColorSpace = CGColorSpaceCreateDeviceRGB()
        let context = CGContext(data: pixelData, width: Int(newImage.size.width), height: Int(newImage.size.height), bitsPerComponent: 8, bytesPerRow: CVPixelBufferGetBytesPerRow(pixelBuffer!), space: rgbColorSpace, bitmapInfo: CGImageAlphaInfo.noneSkipFirst.rawValue) //3
        
        context?.translateBy(x: 0, y: newImage.size.height)
        context?.scaleBy(x: 1.0, y: -1.0)
        
        UIGraphicsPushContext(context!)
        newImage.draw(in: CGRect(x: 0, y: 0, width: newImage.size.width, height: newImage.size.height))
        UIGraphicsPopContext()
        CVPixelBufferUnlockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
        imageView.image = newImage
    }
}

```

高亮代码块做的事情：

1. **#7-11 行：**在方法的前几行中，我们检索了从 `info` 字典中选中的图片（使用 `UIImagePickerControllerOriginalImage` 键）。一旦图片被选中我们就收起 `UIImagePickerController` 。
2. **#13-16行：**由于我们的模型只接受 299x299 规格的图片，我们需要把图片转换成一个正方形。然后，我们将正方形的图片分配给常量 `newImage` 。
3. **#18-23行：**现在，我们把 `newImage` 转换成一个 `CVPixelBuffer` 对象。对于那些不熟悉 `CVPixelBuffer` 的人来说，他就是一个将像素保存在主存中的基本图片缓冲区。
4. **#31-32行：**我们将图片中所有的像素都转换成设备支持的 `RGB` 颜色空间。然后，通过这些数据创建一个 `CGContext` ，我们就可以在需要渲染（或修改）它的一些底层属性时轻松的调用 `context`。
5. **#34-38行：**最后，我们将图形上下文设置为当前上下文，渲染图片，移除栈顶上下文，并把 `imageView.image` 设置为 `newImage` 。

现在，如果你对上面的大部分代码不明白的话，不用担心。这是一些真正的 `Core Image` 高级代码，已经超出本教程的范围了。你只需要知道，我们把图片转成了数据模型可以接受的形式。我建议你多做点相关的联系，看看结果如何，以便于更好地理解。

### 使用 Core ML

不管怎样，让我们把焦点转到 Core ML 上。我们使用了 Inceptionv3 模型去执行物体识别。对于 Core ML，想做到这个，我们只需要几行代码就够了。将下面的代码片段复制到 `imageView.image = newImage` 这一行下面。


```

guard let prediction = try? model.prediction(image: pixelBuffer!) else {
    return
}
 
classifier.text = "I think this is a \(prediction.classLabel)."

```

就这样！ `Inceptionv3` 类已经生成了一个叫做 `prediction(image:)` 的方法，用于预测给定图片中的事物。这里我们通过带有 `pixelBuffer` 变量的方法，该变量就是调整大小后的图片，一旦返回一个 `String` 类型的预测，我们就更新 `classifier` 标签的文本，设置为识别的结果。
 
 现在，是时候测试一下我们的应用了！构建然后在模拟器或者你的手机（安装了 iOS 11 beta 版）上跑起你的应用。从你的相册选择一张图片或者用相机拍摄一张，这个应用将会告诉你图片是关于什么的。
 
 ![]({{  site.url  }}/assets/screenshot/introduction-coreml/p10.jpg)

在测试这个应用的时候，你可能会注意到这个应用并不会正确的预测出你指的是什么。这不是你代码的问题，而是训练模型的问题。

 ![]({{  site.url  }}/assets/screenshot/introduction-coreml/p11.jpg)
 
### 结语
我希望现在你明白怎么把 Core ML 集成到你的应用中。这只是一个入门的教程，如果你有兴趣把你训练好的 Caffe， Keras， 或者 SciKit 模型转换成 Core ML 模型，[继续关注](http://facebook.com/appcodamobile)我们 Core ML 系列的下一篇教程，我将会教你如何把一个模型转换为 Core ML 模型。

对于这个 demo 应用，你可以这 GitHub 上查看[完整的项目](https://github.com/appcoda/CoreMLDemo)。
 
 对于 Core ML 的更多细节，你可以参考[ Core ML 官方文档](https://developer.apple.com/documentation/coreml)，你也可以参考苹果在2017年WWDC上关于 Core ML 的会议：
 
 * [ Core ML 介绍](https://developer.apple.com/videos/play/wwdc2017/703/)
 * [ Core ML 在深度学习领域](https://developer.apple.com/videos/play/wwdc2017/710/)

 
 你认为 Core ML 如何呢？请留下你的评论，让我们知道。
 

