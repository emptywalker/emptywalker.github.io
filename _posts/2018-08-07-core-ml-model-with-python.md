---
layout: post
title: 「译」使用 Python 和 Turi Create 创建一个自定义的 Core ML 模型
date: 2018-08-06 16:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
>  [原文地址](https://www.appcoda.com/core-ml-model-with-python/)


在过去的几年里，使用机器学习的方式去解决问题和执行复杂任务一直在增加。机器学习使我们可以使用大数据去解决复杂任务，比如：图片分类和语音识别。苹果最近宣布一个框架去简化把机器学习模型集成到 macOS, iOS, tvOS 和 watchOS 设备中的过程，这个框架叫做 [**Core ML**](https://developer.apple.com/documentation/coreml) 。苹果也提供了示例 Core ML 模型去测试框架。

当 **Core ML** 首次发布时，对开发者来说，去创建一个自己的 Core ML 模型是一个非常大的挑战，因为需要之前就有机器学习的经验。

然而，感谢 GraphLab 和 Apple ，现在我们有了 [**Turi Create**](https://github.com/apple/turicreate) , 一个使我们可以简单的创建 Core ML 模型的框架。 **Turi Create** 为了可以创建我们自己的机器学习模型，给我们提供了基本的机器学习算法，比如：K-近邻算法，和高级深度学习算法，比如： Residual Networks (ResNet) 。

在本教程中，我将会演示如何创建一个自定义的图片分类器 Core ML 模型，并把它集成到 iOS 应用程序中。为了完成目标，我们将会使用 **Python 2.7** ，**Turi Create** ，**Swift 4.0** 和 **Core ML** 。

在开始之前，你需要以下信息：

* 一个有 64 位处理器的计算机（ x86_64 架构）
* Python ， 2.7 版本：[**这里下载**](https://www.python.org/downloads/release/python-2714/)

### 开始！
为了开始，我们首先需要使用 Python 的包管理器 `pip` 去安装 **Turi Create** 。包管理器是你在机器上安装 Python 时附带的。

为了安装 **Turi Create**，打开终端，输入以下命令：

```ssh
$ pip install turicreate
```

Python 包安装好了之后，我们将通过以下步骤去创建一个新的 Python 项目：

1. 创建一个 `MLClassifier` 文件夹
2. 打开 Xcode ，创建一个新文件

![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p1.png)

3. 从其它组中选一个 Empty 文件

![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p2.png)

4. 文件命名为 `classifier.py`
5. 把它保存到 `MLClassifier` 文件夹中


### 创建一个数据集

在本例中，我们将创建一个机器学习模型，去分类一个图片是包含米还是汤。为了做到这个，我们将要创建一个包含所有种类图片的数据集。这个数据的设置是用于训练我们的模型。你可以[**下载**](https://drive.google.com/file/d/1ZLigrn7YcETalcj2qK6UqXceDdOV3244/view?usp=sharing)我使用的数据集，并保存早 `MLClassifier` 文件夹中。或者你可以通过下面的步骤来创建自己的数据集：
1. 在 `MLClassifier` 文件夹中，创建一个新的 `dataset` 文件夹。
2. 在 `dataset` 里面，创建两个空的文件夹 `rice` 和 `soup` 。
3. 在 `rice` 里面，添加至少 100 张不同大米的图片。
4. 在 `soup` 里面，添加至少 100 张不同汤的图片。

`dataset` 文件夹中的内容应该像下面这样：

![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p3.png)

### 实现 Turi Create

> Turi Create 简化了自定义机器模型的开发过程。你不需要成为一名机器学习的专家，就能为你的 app 添加推荐、对象识别、图片分类、图片相似性或活动分类。
> 
> —— Turi Create [官方文档](https://github.com/apple/turicreate)

在这部分，我们将会在 Python 项目中实现 `turicreate` ，标记我们在前面创建的数据集中的每个图片。让我们来通过以下步骤实现和配置我们的框架。
1. 打开 `classifier.py` 文件，通过下面一行代码导入 `turicreate` ：

```python
import turicreate as turi
```
2. 接下里，定义数据集的路径：

```python
url = "dataset/"
```
3. 然后添加下面的代码去寻找和加载数据集文件夹里的图片：

```python
datad  = turi.image_analysis.load_images(url)
```
4. 继续添加下面一行代码，我们基于它们的路径定义图片的类别：

```python
data["foodType"] = data["path"].apply(lambda path: "Rice" if "rice" in path else "Soup")
```
-给定一张图片，图片分类器的目标就是从确定的标签库中分配一个给它。

5. 最后，保存新标记的数据集为 `rice_or_soup.sframe` ，我们将会使用它去训练模型
```python
data.save("rice_or_soup.sframe")
```

6. 在 Turi Create 中预览新标记的数据集

```python
data.explore()
```

### 训练 & 导出机器学习模型
是时候去训练和导出机器学习模型用于生产！
为了实现这个，我们将使用 `turicreate` 提供的 [**SqueezeNet**](https://apple.github.io/turicreate/docs/api/generated/turicreate.image_classifier.create.html)架构选项来训练我们的机器学习模型。我们使用**SqueezeNet**架构是因为它花费的训练时间更少；然而，为了更准确的结果推荐使用 **ResNet-50** 。接下来，我将使用两种架构来演示示例：
1. 在 `classifier.py` 文件中，我们首先加载前面保存的 `rice_or_soup.sframe` 文件。在文件中插入下面一行代码：

```python
dataBuffer = turi.SFrame("rice_or_soup.sframe")
```

2. 接下来，使用 `dataBuffer` 对象的 90% 创建训练数据，我们只用剩下的 10% 创建测试数据。

```python
trainingBuffers, testingBuffers = dataBuffer.random_split(0.9)
```

3. 继续插入下面的代码，使用训练数据和 `SqueezeNet` 架构去图片分类器。

```pyhton
model = turi.image_classifier.create(trainingBuffers, target="foodType", model="squeezenet_v1.1")
```
另外，你也可以使用 `ResNet-50` 得到一个更精确的结果。

```python
model = turi.image_classifier.create(trainingBuffers, target="foodType", model="resnet-50")
```
4. 接下来，我们将会评估测试数据来决定模型的准确度：

```python
evaluations = model.evaluate(testingBuffers)
print evaluations["accuracy"]
```
5. 最后，插入下面的代码保存模型，并将图片分类器模型导为 Core ML 模型：

```python
model.save("rice_or_soup.model")
model.export_coreml("RiceSoupClassifier.mlmodel")
```

你的 `classifier.py` 文件应该看起来像这样：
![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p4.png)

现在，是时候运行我们的代码了！因此，来到 `Terminal.app` ，切换到 `MLClassifier` 文件夹下，然后执行下面的代码运行 `classifier.py` 文件：

```ssh
python classifier.py
```
只要耐心等待一下，你就会看到下面的信息。
![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p5.png)

几分钟后 ... 你将会有一个 Core ML 模型准备去实现在任何 iOS, macOS, tvOS, 或 watchOS 应用程序中。

### 把 Core ML 模型集成到一个 iOS app
现在我们准备将我们刚刚创建的自定义 Core ML 模型集成到一个 iOS app中。如果你之前已经读过[**我们 Core ML 的入门教程**](https://emptywalker.github.io/2018/07/introduction-core-ml/)，那么你应该对如果集成一个 Core ML 模型到 iOS app 中有一些想法。因此，我将使这部分简短而甜蜜。
1. 使用 Single Application 模板创建一个新的 iOS 项目，可以命名成任何你你喜欢的名字，但在本示例中要确保使用 Swift 。
2. 为了使用我们刚刚创建的模型，把训练好的 Core ML 模型（ `RiceSoupClassifier.mlmodel` 文件）拖到项目中。
![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p6.gif)

3. 在我们的项目中导入 `CoreML` 框架，然后在 `ViewController.swift` 文件中添加下面一行代码。

```swift
import CoreML
```
4. 创建 `CoreML` 模型对象

```swift
let mlModel = RiceSoupClassifier()
```
5. 创建应用程序的 UI
 
```swift
 var importButton:UIButton = {
    let btn = UIButton(type: .system)
    btn.setTitle("Import", for: .normal)
    btn.setTitleColor(.white, for: .normal)
    btn.backgroundColor = .black
    btn.frame = CGRect(x: 0, y: 0, width: 110, height: 60)
    btn.center = CGPoint(x: UIScreen.main.bounds.width/2, y: UIScreen.main.bounds.height*0.90)
    btn.layer.cornerRadius = btn.bounds.height/2
    btn.tag = 0
    return btn
}()
    
var previewImg:UIImageView = {
    let img = UIImageView()
    img.frame = CGRect(x: 0, y: 0, width: 350, height: 350)
    img.contentMode = .scaleAspectFit
    img.center = CGPoint(x: UIScreen.main.bounds.width/2, y: UIScreen.main.bounds.height/3)
    return img
}()
    
var descriptionLbl:UILabel = {
    let lbl = UILabel()
    lbl.text = "No Image Content"
    lbl.frame = CGRect(x: 0, y: 0, width: 350, height: 50)
    lbl.textColor = .black
    lbl.textAlignment = .center
    lbl.center = CGPoint(x: UIScreen.main.bounds.width/2, y: UIScreen.main.bounds.height/1.5)
    return lbl
}()
```
6. 添加 `UIImagePickerController` 协议响应 `ViewController` 类：

```swift
class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationContro
```
7. 创建一个 `importFromCameraRoll` 方法：

```swift
@objc func importFromCameraRoll() {
    if UIImagePickerController.isSourceTypeAvailable(.photoLibrary) {
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        imagePicker.sourceType = .photoLibrary;
        imagePicker.allowsEditing = true
        self.present(imagePicker, animated: true, completion: nil)
    }
}
```
8. 在 `viewDidLoad` 方法中，把导入按钮和导入方法连接起来，然后将按钮添加到子视图中：

```swift
importButton.addTarget(self, action: #selector(importFromCameraRoll), for: .touchUpInside)
self.view.addSubview(previewImg)
self.view.addSubview(descriptionLbl)
self.view.addSubview(importButton)
```
9. 创建一个 `UIImage` 的扩展，添加下面的函数，用于将 `UIImage` 对接转换成 `CVPixelBuffer` ：

```swift
extension UIImage {
    func buffer(with size:CGSize) -> CVPixelBuffer? {
        if let image = self.cgImage {
            let frameSize = size
            var pixelBuffer:CVPixelBuffer? = nil
            let status = CVPixelBufferCreate(kCFAllocatorDefault, Int(frameSize.width), Int(frameSize.height), kCVPixelFormatType_32BGRA , nil, &pixelBuffer)
            if status != kCVReturnSuccess {
                return nil
            }
            CVPixelBufferLockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags.init(rawValue: 0))
            let data = CVPixelBufferGetBaseAddress(pixelBuffer!)
            let rgbColorSpace = CGColorSpaceCreateDeviceRGB()
            let bitmapInfo = CGBitmapInfo(rawValue: CGBitmapInfo.byteOrder32Little.rawValue | CGImageAlphaInfo.premultipliedFirst.rawValue)
            let context = CGContext(data: data, width: Int(frameSize.width), height: Int(frameSize.height), bitsPerComponent: 8, bytesPerRow: CVPixelBufferGetBytesPerRow(pixelBuffer!), space: rgbColorSpace, bitmapInfo: bitmapInfo.rawValue)
            context?.draw(image, in: CGRect(x: 0, y: 0, width: image.width, height: image.height))
            CVPixelBufferUnlockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
            
            return pixelBuffer
        }else{
            return nil
        }
    }
}
```

10. 在 `ViewController` 类中，添加以下方式去分析导入的数据，和将结果显示在 `UILabel` 中：

```swift
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
    if let image = info[UIImagePickerControllerOriginalImage] as? UIImage {
        previewImg.image = image
        if let buffer = image.buffer(with: CGSize(width:224, height:224)) {
            guard let prediction = try? mlModel.prediction(image: buffer) else {fatalError("Unexpected runtime error")}
            descriptionLbl.text = prediction.foodType
            print(prediction.foodTypeProbability)
        }else{
            print("failed buffer")
        }
    }
    dismiss(animated:true, completion: nil)
}
```


### 准备测试
就这样！玩的开心哈。运行 app 然后点击 Import 按钮去测试一下。现在，你可以理解如何去创建一个自定义的机器学习模型，并将它们实现在你的应用程序中！

![]({{  site.url  }}/assets/screenshot/core-ml-model-with-python/p7.gif)

想要下载整个项目，你可以在 [**GitHub 中找到它**](https://github.com/appcoda/CoreMLImage)。关于本文，你如果有任何问题和想法，可以留在在下方的评论区，让我们知道。



