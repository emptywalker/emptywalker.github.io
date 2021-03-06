---
layout: post
title: 「译」用 Swift 4 和 Xcode 9 使用 SceneKit 构建一个简单的 ARKit 应用
date: 2018-08-14 10:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
> 
> 作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-introduction-scenekit/)    -    原文日期：2017-09-19

增强现实( Augmented Reality  )已经来了，它以一种宏大的方式到来。还记得 Pokemon Go 吗？好的，那只是增强现实的一种口味。从 iOS 11 开始，Apple 正在把增强现实带向大众。随着 iOS 11 的到来，数以亿计的 iPhone 和 iPad 设备具备增强现实的功能。一夜之间， 就打造成了世界上最大的 [**ARKit**](https://developer.apple.com/arkit/) 平台，是的 —— 一夜之间。如果你对在 iOS 11 上构建增强现实的 Apps 感兴趣的话，那么你就来对地方了。

### 目标

这篇教程写作的目标是通过使用 SceneKit 构建出一个 ARKit 示例 App ，从而帮助你了解 ARKit 的基本原理。

是时候带你下水了，你会看到构建出一个 ARKit App 的全部过程，并通过你的设备让你和 AR 世界进行交互。

这篇教程的主要想法就是通过构建一个 App 去学习技术和它的 APIs 。通过这个过程，你会明白 ARKit 是如何在真实设备上和你创建的炫酷 3D 对象进行交互的。

在太过奇幻之前，让我们先了解一下非常基础的东西，这也是本教程的目的所在。

### 前提条件

本教程建议你对iOS 开发的基础知识有充分的了解，这是一篇中级教程。你将需要 Xcode 9 或更高版本。

为了测试你的 ARKit App ，你将需要一个部兼容 Apple ARKit 能力的设备，这些设备必须有 Apple A9 或更高版本的处理器。现在，所有东西你都准备好了，且你也合适。让我们开始研究吧！这里有一些我将要带你做的事情：
* 创建一个新的 ARKit Apps 的项目
* 设置  ARKit SceneKit View
* 连接 ARSCNView 和控制器
* 连接 IBOutlet
* 配置 ARSCNView Session
* 允许使用相机
* 给 ARSCNView 添加一个 3D 模型
* 给 ARSCNView 添加自定义手势
* 从 ARSCNView 上移除对象
* 给 ARSCNView 添加多个对象

### 创建一个新的项目

继续，打开 Xcode ，在 Xcode 菜单栏中，选择  File > New > Project… ，选择 Single View App ，然后点击下一步。Xcode 有一个 ARKit 模板，但实际上，你只要使用单视图应用模板就可以构建一个 AR 应用。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p1.png)

你可以给你的项目命名成任何你想要的名字，我命名我的项目为 **ARKitDemo** ，然后按下 next 就创建了你的新项目。

### 设置 ARKit SceneKit View

现在打开 Main.storyboard 。在 Object Library 中找到 ARKit SceneKit View ，把 ARKit SceneKit View 拖到你的视图控制器中。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p2.png)

然后给你的 ARKit SceneKit View 设置约束，充满整个控制器，看起来应该像这样：

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p3.png)

酷喔！这个 ARKit SceneKit View 将是我们使用增强现实展示 SceneKit 内容的地方。

### 连接 IBOutlet

我们仍在 Main.storyboard 文件里，去到工具栏然后打开编辑器助手。在 ViewController.swift 文件的顶部添加一条导入语句去导入 `ARKit` ：

```swift
import ARKit
```

然后按住 control 键，从 ARKit SceneKit View 拖拽到 ViewController.swift 文件中。当弹出弹框的时候，给 IBOutlet 命名为 `sceneView` ，也可以随意删除 `didReceiveMemoryWarning()` 方法，在这篇教程中我们不需要它。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p4.png)

### 配置 ARSCNView Session

我们想 app 一打开就可以通过相机镜头看到世界，并开始检测我们周围的环境。如果你仔细想一想，这是一项非常了不起的技术。 Apple 使增强现实技术成为了可能，开发者不需要从头开发整套技术。感谢 Apple 用 ARKit 造福我们。

好的，是时候来配置 ARKit SceneKit View ，在 ViewController 类中插入以下代码：

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    let configuration = ARWorldTrackingConfiguration()
    sceneView.session.run(configuration)
}
```
在 `viewWillAppear(_:)` 方法里，我们初始化了一个叫做 `ARWorldTrackingConfiguration` 的 AR 配置，这是一个运行世界追踪( world tracking )的配置。

等等，什么是世界追踪？根据 Apple 的文档：
> 「世界追踪提供了 6 个自由角度去跟踪设备。通过在场景中寻找特征点，世界追踪可以对 frame 执行碰撞测试。一旦会话暂停后，追踪无法再恢复。」
> 
> —— Apple 的文档

世界追踪配置跟踪设备的方向和位置，它还可以检测通过设备摄像头看到的真实世界的表面。

现在，我们设置 sceneView 的 AR 会话来运行我们刚刚初始化的配置。 AR 会话管理着运动追踪和视图内容上的相机图像处理。现在，我们在 `ViewController` 里添加另一个方法：

```swift
override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    sceneView.session.pause()
}
```
在 `viewWillDisappear(_:)` 方法中，我们只是告诉我们的 AR 会话停止跟踪动作和视图内容上的图片处理。

### 允许使用相机

现在，在我们运行我们的应用程序之前，我们需要告知用户我们将利用他们设备的相机来进行增强现实，自从 iOS 10 发布之后，这就是一个必要条件。因此，打开 Info.plist 。在空白区域点击右键，选择 *Add row* ，设置键为 *Privacy — Camera Usage Description* ，值为 *For Augmented Reality* 。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p5.png)

继续进行之前，让我们来确保在这点上你所有事情都设置正确了。

拿出你的设备，把它连接到你的 Mac 上，在 Xcode 上编译并运行你的项目。 App 会提示你是否允许访问相机，点击 OK 。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p6.png)

现在，你应该可以看到你的相机视图了。

我们已经配置了 sceneView 的会话去运行世界追踪配置。是时候退出这一部分了，增强现实！

### 给 ARSCNView 添加 3D 对象

已经来到我们一直等待的那一刻了。

不在废话了，让我们增强现实。我们首先添加一个盒子，在 `ViewController` 类中，插入以下代码：

```swift
func addBox() {
    let box = SCNBox(width: 0.1, height: 0.1, length: 0.1, chamferRadius: 0)
    
    let boxNode = SCNNode()
    boxNode.geometry = box
    boxNode.position = SCNVector3(0, 0, -0.2)
    
    let scene = SCNScene()
    scene.rootNode.addChildNode(boxNode)
    sceneView.scene = scene
}
```

这就是我们所做的。

我们一开始创建一个 box 的外形，1 浮点 = 1 米。

然后，我们创建了一个节点，一个节点代表这个对象在 3D 空间中的位置和坐标。节点本身没有可视内容。

通过给节点一个外形，我们就可以给节点一个可视化的内容。我们通过把节点的几何结构设置成盒子来实现这个。

然后，我们给节点一个位置，这个位置是相对于摄像头的。正的 x 表示右边，负的 x 表示左边，正的 y 是上，负的 y 是下，正的 z 是向后，负的 z 是向前。

然后我们创建了一个场景，这个 SceneKit 的场景是用来显示在视图上的。然后，我们把盒子节点添加到场景的根节点上。一个场景中的根节点，定义了通过 SceneKit 渲染的真实世界的坐标系。

一般来说，我们的场景中现在已经有了一个盒子，这个盒子位于摄像头的中心位置，它相对于相机来说向前 0.2 米。

最后，我们设置 sceneView 的场景来显示我们刚刚创建的场景。

现在，在 `viewDidLoad()` 中调用 `addBox()` ：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    addBox()
}
```

编译和运行 App ，你应该可以看到一个悬浮框！

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p7.png)

你也可以简单的重构 `addBox()` ：

```swift
func addBox() {
    let box = SCNBox(width: 0.05, height: 0.05, length: 0.05, chamferRadius: 0)
    
    let boxNode = SCNNode()
    boxNode.geometry = box
    boxNode.position = SCNVector3(0, 0, -0.2)
    
    sceneView.scene.rootNode.addChildNode(boxNode)
}
```

单独地解释一些组件更容易点。

好的，是时候来添加手势了。

### 向 ARSCNView 添加手势识别器
在 `addBox()` 方法正下面，添加以下代码：

```swift
func addTapGestureToSceneView() {
    let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(ViewController.didTap(withGestureRecognizer:)))
    sceneView.addGestureRecognizer(tapGestureRecognizer)
}
```
这里，我们初始化了一个轻击手势识别器， target 设为 ViewController ，行为选择子设置为 `didTap(withGestureRecognizer:)` 作为回调函数。然后，我们把这个轻击手势识别器添加到 sceneView 上。

现在，是时候在轻击手势识别器的回调函数中做点事了。

### 从 ARSCNView 中移除对象

在 `ViewController.swift` 文件中插入一下代码：

```swift
@objc func didTap(withGestureRecognizer recognizer: UIGestureRecognizer) {
    let tapLocation = recognizer.location(in: sceneView)
    let hitTestResults = sceneView.hitTest(tapLocation)
    guard let node = hitTestResults.first?.node else { return }
    node.removeFromParentNode()
}
```
这里，我们创建了一个 `didTap(withGestureRecognizer:)` 方法。我们检测用户相对于 sceneView 的点击位置，并点击测试看看我们是否击中任何节点。

然后，我们从 `hitTestResults` 中安全解包第一个节点，如果结果中包含了至少一个节点，我们将从它的父节点中删除我们点中的第一个节点。

在我们测试对象移除之前，更新一下 `viewDidLoad()` 方法，添加一个调用的 `addTapGestureToSceneView()` 的方法：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    addBox()
    addTapGestureToSceneView()
}
```
现在，如果你的编译和运行你的项目，你应该可以点击盒子节点，然后它将会从场景视图中移除。

看起来我们又回到了原地了。

OK 。现在是时候添加多个对象了。

### 给 ARSCNView 添加多个对象

现在我们的盒子是有点孤单的，让我们给盒子多一点盒子。我们要从特征点检测中添加对象。

因此，什么是特征点？

根据 Apple 的说法，下面是特征点的定义：

> 由 ARKit 自动识别为连续曲面的一部分但没有相应锚点的点。

它基本上是现实世界事物表面上的监测点。回到添加盒子的实现中来，开始之前，让我们在 `ViewController` 类的结尾处添加一个扩展：

```swift
extension float4x4 {
    var translation: float3 {
        let translation = self.columns.3
        return float3(translation.x, translation.y, translation.z)
    }
}
```
这个扩展基本上将一个矩阵转成 `float3` 。它从矩阵中给了我们 x ， y 和 z 。

同时，我们需要把 `addBox()` 修改成下面这样：

```swift
func addBox(x: Float = 0, y: Float = 0, z: Float = -0.2) {
    let box = SCNBox(width: 0.1, height: 0.1, length: 0.1, chamferRadius: 0)
    
    let boxNode = SCNNode()
    boxNode.geometry = box
    boxNode.position = SCNVector3(x, y, z)
    
    sceneView.scene.rootNode.addChildNode(boxNode)
}
```
我们主要在最初的 `addBox()` 函数中添加了参数。我们也给他一个默认参数值，这就意味着我们调用 `addBox()` 不需要传入 x ， y ， z 坐标，就像 `viewDidLoad()` 中一样。

酷！

现在，我们需要修改 `didTap(withGestureRecognizer:)` 方法。如果有一个特征点被检测到了我们就去添加一个物体。

因此，在 `guard let` 语句里面， `return` 语句之前。添加以下代码：

```swift
let hitTestResultsWithFeaturePoints = sceneView.hitTest(tapLocation, types: .featurePoint)
 
if let hitTestResultWithFeaturePoints = hitTestResultsWithFeaturePoints.first {
    let translation = hitTestResultWithFeaturePoints.worldTransform.translation
    addBox(x: translation.x, y: translation.y, z: translation.z)
}
```
这就是我们正在做的事情。

首先，我们执行了命中测试，类似于我们第一次执行的方式。除了，我们给 `types` 参数指定了一个 `.featurePoint` 结果类型。 `types` 要求点击测试搜寻一个真实世界的物体或者通过 AR 会话的相机图像处理检测到的平面，结果类型的类型有很多。然而，在本教程中我们只会聚焦在特征点。

特征点点击测试之后，我们安全地解包了第一个点击测试的结果。这是很重要的因为并不总会有特征点的。 ARKit 可能不总是会检测到一个真实世界的物体或者在真实世界的平面。

如果第一个点击测试的结果可以被安全的解包。然后，我们将类型为 `matrix_float4x4` 的矩阵转为 `float3` 。这是可以的，因为我们之前创建了一个扩展。

我们然后在点击检测到的特征点之前使用 x ， y 和 z 添加一个新的盒子。

你的 `didTap(withGestureRecognizer:)` 方法，看起来应该像这样：

```swift
@objc func didTap(withGestureRecognizer recognizer: UIGestureRecognizer) {
    let tapLocation = recognizer.location(in: sceneView)
    let hitTestResults = sceneView.hitTest(tapLocation)
    guard let node = hitTestResults.first?.node else {
        let hitTestResultsWithFeaturePoints = sceneView.hitTest(tapLocation, types: .featurePoint)
        if let hitTestResultWithFeaturePoints = hitTestResultsWithFeaturePoints.first {
            let translation = hitTestResultWithFeaturePoints.worldTransform.translation
            addBox(x: translation.x, y: translation.y, z: translation.z)
        }
        return
    }
    node.removeFromParentNode()
}
```

### 试用最终 App
编译然后运行你的项目。除了之前有的效果之外，你现在应该可以在特征点上添加一个盒子。

![]({{  site.url  }}/assets/screenshot/simple-arkit-demo/p8.png)

### 总结

恭喜你可以看到这里。 ARKit 自身是一个巨大的话题，我们已经学习了很多的基本原理。然而，我们只是接触了一点 ARKit 的表面，我希望你能够从这篇 ARKit 的介绍中获益。我已经迫不及待的想看看像你们这样的开发者将去构建疯狂的 ARKit App 想法了。

对于完整的项目，你可以在 [**GitHub**](https://github.com/appcoda/SimpleARKitDemo) 中找到它。

如果你愿意学习更多的关于 ARKit 的知识，通过把这篇教程分享给你的朋友们，来让我们知道。