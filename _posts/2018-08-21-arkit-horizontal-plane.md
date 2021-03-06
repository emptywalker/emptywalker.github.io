---
layout: post
title: 「译」ARKit 教程：使用 SceneKit 检测水平面并添加 3D 对象
date: 2018-08-21 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-horizontal-plane/)    -    原文日期：2017-12-11



增强现实有能力以前所未有的方式放大世界。我们与世界互动的方式可能再也不会相同。随着 iPhone X 的发布，世界现在比以往任何时候都更愿意接受 AR 。我们正处于历史上的一个特殊时期，而且刚刚开始出现**巨大的**变化。 AR 的潜力无穷无尽。

### 前提条件

本篇教程是建立在上一篇 ARKit 教程的知识之上的。如果你没有准备好，你可以查看一下上一篇[**教程**](https://emptywalker.github.io/2018/08/arkit-3d-object/)。如果你能为你的应用找到一个平面也会很棒。

### 我们将要学习知识

在本教程中，我们焦点是 ARKit 中的水平面，我们将先创建一个海洋（水平面），然后在上面放一个漂亮的船（ 3D 对象）。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p1.jpg)

或者创建一个有光照的舰队。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p2.jpg)

在这个过程中，你将会学习 ARKit 中的水平面。我的希望是通过学完本教程，当你在 ARKit 项目上工作时，你会感觉到利用水平面更加舒适。

### 水平面是什么

那么，当我们在讨论 ARKit 中的水平面时，我们到底在讨论什么呢？当我们用 ARKit 来检测一个水平面时 —— 技术上我们检测一个 ARPlaneAnchor 。那什么是一个 ARPlaneAnchor 呢？一个 ARPlaneAnchor 是一个包含了已检测到的水平面信息的对象。

这是一个 ARPlaneAnchor 更正式的描述，来自 Apple ：

> 关于在一个世界追踪 AR 会话中检测到一个真实世界平面位置和方向的信息。
> —— Apple 文档

### 开始构建一个 App
我们将从一个[**启动项目**](https://github.com/appcoda/ARKitHorizontalPlaneDemo/raw/master/ARKitHorizontalPlaneDemoStarter.zip)开始，因此我们可以专注于 ARKit 的实现。用 Xcode 打开启动项目，并浏览一下。我已经在
故事板中创建了一个 ARSCNView 。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p3.png)

编译和运行启动项目进行一个快速测试。你应该会在你的设备中看到以下提示：
![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p4.png)

确保你点击 OK 同意访问相机，然后你会看到你的相机视图。


### 水平面检测

检测一个水平面很简单，感谢 「appley」 Apple 工程师。

简单的在 `ViewController` 的 `setUpSceneView()` 方法里面添加下面的代码：

```swift
configuration.planeDetection = .horizontal
```

通过把 `ARWorldTrackingConfiguration` 的 `planeDetection` 属性设置成 `.horizontal`，告诉 ARKit 去寻找任意的水平面。一旦 ARKit 检测到了一个水平面，那个水平面将会被添加到 `sceneView` 的会话上。

为了检测水平面，我们不得不采用 `ARSCNViewDelegate` 协议。在 `ViewController` 类的下面，创建一个 `ViewController` 类的扩展去实现协议：

```swift
extension ViewController: ARSCNViewDelegate {
 
}
```
现在，在类的扩展中，实现 `renderer(_:didAdd:for:)` 方法：

```swift
func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
 
}
```

每次场景视图的会话有一个新的 ARAnchor 添加后这个协议方法就会被调用，ARAnchor 是在 3D 空间表示一个物理位置和方向的对象。我们稍后将使用 ARAnchor 去检测一个水平面。

接下来，回到 `setUpSceneView()` ，在 `setUpSceneView()` 里面，把 `sceneView` 的代理设为你的 `ViewController` 。

如果你愿意，你也可以设置 `sceneView` 的调试选项去在世界中显示特征点。这可以帮助你找到一个有足够特征点的地方，去检测水平面。一个水平面是由很多个特征点组成。一旦检测到了足够多的特征点识别到了一个水平平面， `renderer(_:didAdd:for:)` 方法将会被调用。

你的 `setUpSceneView()` 方法现在看起来应该像这样：

```swift
func setUpSceneView() {
    let configuration = ARWorldTrackingConfiguration()
    configuration.planeDetection = .horizontal
    
    sceneView.session.run(configuration)
    
    sceneView.delegate = self
    sceneView.debugOptions = [ARSCNDebugOptions.showFeaturePoints]
}
```

### 水平面展示
现在，每次一个新的 ARAnchor 被添加到 `sceneView` 上 app 就会得到一个通知，我们可能对看看新添加的 ARAnchor 是什么样子比较感兴趣。

于是，更新 `renderer(_:didAdd:for:)` 方法像下面这样：

```swift
func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
    // 1
    guard let planeAnchor = anchor as? ARPlaneAnchor else { return }
    
    // 2
    let width = CGFloat(planeAnchor.extent.x)
    let height = CGFloat(planeAnchor.extent.z)
    let plane = SCNPlane(width: width, height: height)
    
    // 3
    plane.materials.first?.diffuse.contents = UIColor.transparentLightBlue
    
    // 4
    let planeNode = SCNNode(geometry: plane)
    
    // 5
    let x = CGFloat(planeAnchor.center.x)
    let y = CGFloat(planeAnchor.center.y)
    let z = CGFloat(planeAnchor.center.z)
    planeNode.position = SCNVector3(x,y,z)
    planeNode.eulerAngles.x = -.pi / 2
    
    // 6
    node.addChildNode(planeNode)
}
```

让我带你一行一行的看看代码：

1. 我们安全地解包了 anchor 参数成为一个 ARPlaneAnchor ，以确保我们掌握到了有关检测到的真实世界平面的信息。
2. 于是，我们创建了一个 SCNPlane 去显示 ARPlaneAnchor ， SCNPlane 就是一个单面的矩形平面几何体，我们解包了 ARPlaneAnchor  extent 的 x 和 z 属性，使用它们创建了一个 SCNPlane 。一个 ARPlaneAnchor 的 extent 是在真实世界中检测到的平面的估算大小。我们提取 extent 的 x 和 z 作为我们 SCNPlane 的高和宽。然后我们给平面一个透明的浅蓝色去模拟水。
3. 我们使用刚刚创建的 SCNPlane 几何体创建一个 SCNNode 。
4. 我们初始化 x ， y 和 z 常量表示 `planeAnchor` 的中心 x ， y 和 z 的位置。这就是我们 planeNode 的位置。我们在逆时针方向将 planeNode 的 x 的 euler 角度旋转 90 度，否则 `planeNode` 将会垂直于桌面。如果顺时针， David Blaine 将会表现出一种神奇的错觉，因为 SceneKit 默认使用一边的材料渲染 SCNPlane 表面。
5. 最后，我们把 planeNode 作为子节点添加到新添加的 SceneKit 节点上。

编译和运行你的项目，你现在应该可以检测和展示检测到的水平面了。
![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p5.jpg)

### 水平面扩张
随着 ARKit 收到额外的关于我们周围环境的信息，我们可能会想去扩张我们之前检测到的平面，去制作一个更大的表面或者用新的信息可以获取更准确的表示。

于是，实现 `renderer(_:didUpdate:for:)` ：

```swift
func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
 
}
```

每次更新 SceneKit 节点属性去匹配它对应的锚点时，都会调用此方法。这就是 ARKit 改进其对水平面位置和范围预估的地方。

参数 `node` 可以让我们去更新锚点的位置。 参数 `anchor` 可以让我们更新锚点的宽和高。用这两个参数，我们可以更新之前实现的 SCNPlane ，以使用更新后的宽和高反映更新后的位置。

接下来，我们在 `renderer(_:didUpdate:for:)` 里面添加以下代码：

```swift
// 1
guard let planeAnchor = anchor as?  ARPlaneAnchor,
    let planeNode = node.childNodes.first,
    let plane = planeNode.geometry as? SCNPlane
    else { return }
 
// 2
let width = CGFloat(planeAnchor.extent.x)
let height = CGFloat(planeAnchor.extent.z)
plane.width = width
plane.height = height
 
// 3
let x = CGFloat(planeAnchor.center.x)
let y = CGFloat(planeAnchor.center.y)
let z = CGFloat(planeAnchor.center.z)
planeNode.position = SCNVector3(x, y, z)
```
同样的，让我带你看看上面的代码做了什么：
1. 首先，我们把 anchor 安全解包成 `ARPlaneAnchor` 类型，接着我们安全解包了 `node` 的第一个子节点，最后，我们将 `planeNode` 的 geometry 安全解包为 `SCNPlane` 类型。我们仅仅获取前面实现的 `ARPlaneAnchor` ， `SCNNode` 和 `SCNplaneand` ，并用对应的参数更新它们的属性。
2. 这里，我们使用 `planeAnchor` 的 extent 的 x 和 z 属性去更新了 `plane` 的宽度和高度。
3. 最后，我们把 `planeNode` 的位置更新成 `planeAnchor` 的 center 的 x ， y 和 z 坐标。

编译运行检查一下水平面扩展的实现效果。

![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p6.gif)

### 添加对象到水平面上

现在，让我们在水平面上添加一条船，在启动项目中，我已经打包了一个 3D 船对象给你使用。

在 `ViewController` 类中插入下面的方法在水平面上放一艘船。

```swift
@objc func addShipToSceneView(withGestureRecognizer recognizer: UIGestureRecognizer) {
    let tapLocation = recognizer.location(in: sceneView)
    let hitTestResults = sceneView.hitTest(tapLocation, types: .existingPlaneUsingExtent)
    
    guard let hitTestResult = hitTestResults.first else { return }
    let translation = hitTestResult.worldTransform.translation
    let x = translation.x
    let y = translation.y
    let z = translation.z
    
    guard let shipScene = SCNScene(named: "ship.scn"),
        let shipNode = shipScene.rootNode.childNode(withName: "ship", recursively: false)
        else { return }
    
    
    shipNode.position = SCNVector3(x,y,z)
    sceneView.scene.rootNode.addChildNode(shipNode)
}
```

这里有很多熟悉的面孔，如前一篇教程所述，因此我不会一行一行的带你仔细阅读代码。如果你想学习这个可以[**查看前一篇教程**](https://emptywalker.github.io/2018/08/arkit-3d-object/)。现在唯一不同的是我们给 `types` 参数传入了不同的值，去在 `sceneView` 里检测已经存在的平面锚点。

锦上添花之前，我们添加下面的代码：

```swift
func addTapGestureToSceneView() {
    let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(ViewController.addShipToSceneView(withGestureRecognizer:)))
    sceneView.addGestureRecognizer(tapGestureRecognizer)
}
```
这个方法会给 `sceneView` 添加一个点击手势识别器。

为了锦上添花，在 `viewDidLoad()` 方法里面调用以下方法在 `sceneView` 上添加一个点击手势识别器：

```swift
addTapGestureToSceneView()
```
现在如果你编译运行，你应该可以检测到一个水平面，可视化它，并把一艘贼帅的船放在上面。
![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p7.gif)

或者一个舰队（有光照）。
![]({{  site.url  }}/assets/screenshot/arkit-horizontal-plane/p8.jpg)

在可以通过解注释 `viewDidLoad()` 方法中的 `configureLighting()` 来启用光照。

启用光照的方法很简单只需要 2 行代码：

```swift
sceneView.autoenablesDefaultLighting = true
sceneView.automaticallyUpdatesLighting = true
```

### 结语
我希望你可以享受并能从这篇教程中学到一些有价值的东西。如果你有，请通过分析这篇教程来让我知道。最后，如果你有任何意见，问题或建议，随意在下面留下它们。

我不确定一个人是否能够做到这个，但你可以使用图片评论，我很想知道你们把船放在了哪里！

为了参考，你可以在 [**GitHub 上下载最终的项目**](https://github.com/appcoda/ARKitHorizontalPlaneDemo)。
