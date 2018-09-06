---
layout: post
title: 「译」ARKit 教程：实现 2D 图片识别
date: 2018-09-03 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-image-recognition/)    -    原文日期：2018-04-18


欢迎来到我们 ARKit 教程系列的第六部分。这周我们将讨论在增强现实中实现图片识别。从 iOS 11.3 开始， ARKit 已经有能力识别 2D 图像了。如果你对学习关于构建一个使用 ARKit 识别 2D 图像的 App 感兴趣的话，那么这篇教程就是为你而写的。

> 通过使用用户环境的已知特征来触发虚拟内容的外观，可以增强许多 AR 体验。例如，一个博物馆 app ，当用户用设备对着一副画的时候，可以展示一个虚拟馆长，或者对于一个棋盘游戏，当玩家将设备指向一个棋盘时，可以放一个些虚拟的棋子。在 iOS 11.3 和 之后版本，你可以添加这样的功能到你的 AR 体验中，通过启用 ARKit 的图片识别：你的 App 提供识别 2D 图像， ARKit 会在 AR 会话过程中告诉你这些图像在哪里被识别。
> 
> —— Apple 文档 


### 前提条件

这篇教程需要你对[**我们上一篇讨论的 ARKit 教程里的话题**](https://emptywalker.github.io/2018/08/arkit-light-estimation/)有个深入的理解。如果你是一个 ARKit 新手，请在这里查看[**我们的 ARKit 教程系列**](https://www.appcoda.com/tag/arkit/)。

为了跟着这篇教程，你将需要一个 9.3 或更高的 Xcode 和跑着 iOS 11.3 或更高版本的 Apple 设备。

不多说了，我们开始。

### 你将构建的效果

我们将构建一个 ARKit 图片识别 App 。在任何时候，App 检测到一个可识别的图像，它将会运行一个动画去在真实世界中展示检测到的图片的位置和大小。在动画的上面， App 将会有一个 label ，显示检测到的图片的名字。如果你不明白我的意思，下面的图片将会让你更好理解。

![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p1.gif)


### 开始

首先，通过在[**这里**](https://raw.githubusercontent.com/appcoda/ARKitImageRecognition/master/StarterProject.zip)下面启动项目来开始。启动项目已经提前构建了 UI 元素和行为方法。这样，我们就可以关注在 ARKit 图片识别核心元素上了。

> **提示：**如果你想学习更多关于如何创建专业 UI 元素，请随意查看 [**Beginner iOS Programming with Swift Book**](https://www.appcoda.com/swift/) 和 看看更高级的 [** Intermediate iOS Programming with Swift Book**](https://www.appcoda.com/intermediate-swift-programming-book/) 。
> 

![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p2.png)

一旦你下载了启动项目，在你的 iOS 设备上编译运行它。你应该被提示去允许在你的 App 中访问相机。点击 OK 运行在你的 App 访问相机。

漂亮。现在，让我们进入 ARKit 图片识别的准备图片阶段。

### 启用 ARKit 的图片识别
为了 ARKit 能够识别图片，你首先需要提供两件事：

1. 被你 App 识别的图片
2. 图片的物理大小

我们通过提供图像本身开始第一步。在启动项目里面，点击 **Assets.xcassets** 。然后，你应该可以看到 **AR Resources** 文件夹。 点击这个文件夹，里面应该有三个图片。
![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p3.png)
你可以可以把你自己的图片拖到这个文件夹里。但确保也给图片一个描述性的名字。

像前面提到的一样，在你的项目中有图像文件自身在 ARKit 图片识别的准备中仅仅是第一步。另外，你还需要提供物理图像大小。

让我们进入下一部分来讨论一下物理图像大小。

### 物理图像大小
ARKit 需要知道在世界里的物理图片大小去决定从图片到相机的距离。输入一个错误的物理图像代销会导致一个到相机错误距离的 `ARImageAnchor` 。

记住每次添加一个新的图像给 ARKit 识别的时候都提供一个物理图片大小。这个值会在测量世界的时候影响图像大小。例如，「书」图像的物理大小如下：
![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p4.png)

这是当图像文件在 15.4 寸 MacBook Pro 显示器被打开预览的物理图像大小属性。你可以在图像的属性检查器中相应地设置物理图像大小属性。

### 图像属性

ARKit 的图像识别能力可能会随便图像属性而变化。看一下 **AR Resources** 文件夹里面的图像。你将会看到 「书」 图像有两个质量评估警告。添加参考图片时请注意这一点。当图像具有高识别度的时候，图像检测效果最好。
![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p5.png)

「 Snow Mountain 」和「 Trees In The Dark 」图像没有黄色警告。这就意味着 ARKit 认为这些图像时容易识别的。
![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p6.png)

不管是否有黄色警告，最好还是测试一下你计划在世界上使用的图像。然后，你自己就可以看到哪些图像时容易辨认的。

接下来，我们将着手写一些代码。

### 图像识别的配置
我们来设置场景视图的配置去检测 **AR Resources** 文件夹里面引用的图像。配置将会重置追踪和移除存在的锚点运行选项。在运行配置的场景视图会话之后，我们使用 App 使用说明更新标签文本。

打开 `ViewController.swift` 文件，在 `ViewController` 类中插入下面的方法：

```swift
func resetTrackingConfiguration() {
    guard let referenceImages = ARReferenceImage.referenceImages(inGroupNamed: "AR Resources", bundle: nil) else { return }
    let configuration = ARWorldTrackingConfiguration()
    configuration.detectionImages = referenceImages
    let options: ARSession.RunOptions = [.resetTracking, .removeExistingAnchors]
    sceneView.session.run(configuration, options: options)
    label.text = "Move camera around to detect images"
}
```
接下来，在 `viewWillAppear(_:)` 和 `resetButtonDidTouch(_:)` 方法里调用 `resetTrackingConfiguration()` 方法。

### 使用 ARImageAnchor 识别图片
我们将会在新检测到的图像上面盖一层白色透明的平面。这个平面将会映射出新检测的参考图像的形状和大小，以及图像到设备相机的距离。当一个新节点被映射到给定锚点的时候，平面挡板 UI 就会出现。

> **提示：** 锚点是 ARImageAnchor 类型，在 *renderer(_:didAdd:for:)* 方法里面。
>
像这样更新 `renderer(_:didAdd:for:)` 方法：

```swift
func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
    guard let imageAnchor = anchor as? ARImageAnchor else { return }
    let referenceImage = imageAnchor.referenceImage
        let imageName = referenceImage.name ?? "no name"
        
    let plane = SCNPlane(width: referenceImage.physicalSize.width, height: referenceImage.physicalSize.height)
    let planeNode = SCNNode(geometry: plane)
    planeNode.opacity = 0.20
    planeNode.eulerAngles.x = -.pi / 2
    
    planeNode.runAction(imageHighlightAction)
}
```
平面节点设置运行一个带有淡入淡出动画的 `SCNAction` 序列。
现在，我们有平面节点和检测图像的名字，我们将要把平面节点添加到节点参数上，并把标签文本设置成识别到的图像的名字。在 `planeNode.runAction(imageHighlightAction)` 后面插入以下代码：

```swift
node.addChildNode(planeNode)
DispatchQueue.main.async {
    self.label.text = "Image detected: \"\(imageName)\""
}
```

非常棒！ 你已经得到自己新创建的 ARKit 图像识别 App 。

### 测试 App 
为了演示，你可以将 **AR Resources** 文件夹中的每个图像打印出一个复印件，或者，你可以通过打开图像文件预览来测试它。
![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p7.gif)

让我们转到下一轮，在检测到的图像上放 3D 对象。

### 在检测到的图像上叠加 3D 对象

现在，我们已经在真实世界里可视化了检测到的图像的大小和位置，让我们在检测到图像叠加 3D 对象。

首先，注释下面代码把注意力放到在检测到的图像上叠加 3D 对象。

```swift
let planeNode = self.getPlaneNode(withReferenceImage: imageAnchor.referenceImage)
planeNode.opacity = 0.0
planeNode.eulerAngles.x = -.pi / 2
planeNode.runAction(self.fadeAction)
 
node.addChildNode(planeNode)
```

接下来，替换 TODO ：叠加 3D 对象，用以下代码覆盖掉注释的代码。

```swift
let overlayNode = self.getNode(withImageName: imageName)
overlayNode.opacity = 0
overlayNode.position.y = 0.2
overlayNode.runAction(self.fadeAndSpinAction)
 
node.addChildNode(overlayNode)
```

根据图像检测，你现在应该可以从检测到的图像到朝向你的地方看到一个 SceneKit 节点，伴随着一个淡出和旋转的动画序列。

![]({{  site.url  }}/assets/screenshot/arkit-image-recognition/p8.gif)