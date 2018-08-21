---
layout: post
title: 「译」ARKit 教程：理解和实现 3D 对象
date: 2018-08-17 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-3d-object/)    -    原文日期：2017-11-10



拥有炫酷的视觉元素可以给你的 App 带来独一无二的个性。在本篇教程中，我们将着眼于 3D 对象的创建工具箱、3D 对象的线上资源、 SceneKit 支持的格式，最重要的是，去学习使用 SceneKit 创建一个非常简单的 ARKit app 。

不再废话了，我们制作一些 3D 对象，这将会很有趣！我希望你能够享受本篇教程，感谢分享上一篇 ARKit 教程的每个人。


在我们进行之前，需要注意一下前提条件。我们将会建立在上一篇 ARKit 教程的知识点上，如果你是一个 ARKit 新手，请[**查看入门教程**](https://emptywalker.github.io/2018/08/simple-arkit-demo/)，先学习一下如何使用 SceneKit 创建一个简单的 ARKit App 。

### 你将会学到什么。

好的！我们将会一起经历非常多的事情，以下是你将会在本篇教程中学习到的话题：

* 一个简短的介绍，创建 3D 对象工具箱和资源
* 如何实现一个单节点的 3D 对象
* 如果添加基本光线
* 如何实现一个多节点的 3D 对象

### 如何创建和寻找 3D 对象？

当我们要去渲染一些 3D 对象时，我们首先要面对的就是该如何去创建它们或寻找资源。

#### 3D 对象创建工具箱
![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p1.jpg)

首先，你可以使用 3D 对象的创造工具箱去从头开始创造一个 3D 对象。或者，你也可以用这些工具箱将一些现有的特定文件格式的 3D 对象导出成一个 SceneKit 可以兼容的。稍后我们将会接触更多 SceneKit 支持的格式。如果你计划创建自己的 3D 对象，下面有一些工具你可以看一下：
* [**Blender**](https://www.blender.org/)
* [**SketchUp**](https://www.sketchup.com/)
* [**Autodesk 3ds Max**](https://www.autodesk.com/products/3ds-max/overview)


#### 3D 对象的在线资源

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p2.png)

相对于创建自己的 3D 对象，你可能更喜欢为你的 ARKit app 获得按需的 3D 模型，下面是一些你可能会发现有用的在线资源：
* [**SketchFab**](https://sketchfab.com/)
* [**TurboSquid**](https://www.turbosquid.com/)
* [**Blocks**](https://vr.google.com/blocks/)


### SceneKit 支持的格式
我们将会使用 SceneKit 去构建 ARKit App ，为了把 3D 对象加载到 ARKit App 上， Xcode 需要以 SceneKit 支持的格式读取你的 3D 对象文件。这样才能制作场景。

> SceneKit 可以从一个已支持格式的文件中读取场景内容，或者从持有此类内容的 NSData 对象读取。
>  —— Apple 文档
>

在本篇教程中，我们稍等会提到的两种 SceneKit 支持的格式是 *SceneKit Scene (.scn)* 和 *Digital Asset Exchange (.dae)* 。

### 从启动项目开始

为了开始，首先[**下载启动项目**](https://github.com/appcoda/ARKit3DDemo/raw/master/ARKit3DDemoStarter.zip)。我已经搭建了 App 的框架和打包了两个 3D 文件，因为我们可以专注于 ARKit 的实现。一旦下载了，用 Xcode 打开它，并运行它，快速测试一下。

你应该可以在你的 iOS 设备上看到以下效果：
![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p3.png)

点击 OK ，你应该可以看到你的摄像头的场景。如果你正好在 Apple Store 写代码，就会像下面这样：

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p4.png)

### 实现一个单节点的 3D 对象

非常棒！现在，是时候在我们的 ARSCNView 上添加一个单节点的 3D 对象了，在 `ViewController` 类中插入下面的方法：

```swift
func addPaperPlane(x: Float = 0, y: Float = 0, z: Float = -0.5) {
    guard let paperPlaneScene = SCNScene(named: "paperPlane.scn"), let paperPlaneNode = paperPlaneScene.rootNode.childNode(withName: "paperPlane", recursively: true) else { return }
    paperPlaneNode.position = SCNVector3(x, y, z)
    sceneView.scene.rootNode.addChildNode(paperPlaneNode)
}
```
在上面的代码中，我们首先初始化和安全解包了 **paperPlane.scn** 文件的 **SCNScene** 对象，这是打包在启动项目中的 3D 对象。

接下来，我们使用名为 **paperPlane** 的节点初始化和安全解包一个 **SCNNode** 对象。同事我们把 `recursively` 参数设置为 `true`
， `recursively` 参数决定 SceneKit 是否使用前序遍历来搜索子节点子树。

> 所有节点内容 —— 节点，几何体及其材质，光线，相机，相关对象都组织在具有单个公共根节点的节点层次结果中。
> Apple 文档


一旦节点被初始化了，我们设置 `paperPlaneNode` 的位置为参数 `x` 、 `y` 和 `z` 。默认位置是 0 向量，意味着这个节点位置父节点坐标系中的原点。在这个方法中，我们把 `z` 的默认值设为 `-0.5` ，意味着该对象被放在相机之前。

最后，我们把 `paperPlaneNode` 添加到 `sceneView` 的 `sceneView` 上。

现在，在 `viewDidLoad()` 方法中调用 `addPaperPlane(x:y:z:)` ：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    addPaperPlane()
}
```

在你的设备上编译和运行，你可以看到一个立体的白纸飞机。

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p5.png)

当前，这个纸飞机有点难以显现，这是因为缺少光线和阴影。在真实世界中，我们看到的物体都是有光线和阴影的。光线和阴影帮我们显示 3D 对象。

如果你打开 3D Objects 文件夹下的 **paperPlane.scn** 文件，你可以看到纸飞机是立体白色的。因此它没有视觉深度。

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p6.png)

因此，让我们在纸飞机上添加一些光线！

### 添加基础光线

添加光线不止一种方式，对于本教程，我们将关注自动照明和自动更新照明。

在 `ViewController` 类，添加以下方法：

```swift
func configureLighting() {
    sceneView.autoenablesDefaultLighting = true
    sceneView.automaticallyUpdatesLighting = true
}
```
酷！这就是我们刚刚做的：

* 我们创建了一个 `configureLighting()` 方法。
* 在方法里面，我们设置了 `sceneView` 的 `autoenablesDefaultLighting` 属性为 `true` 。如果 `autoenablesDefaultLighting` 属性被设为 true ，SceneKit 会自动给 scene 添加光线。专业上说，当渲染包含无光线或只包含周围光线的场景的时候， SceneKit 会自动添加和摆放一个全方位的光线资源。
* 接下来，我们设置 `sceneView` 的 `automaticallyUpdatesLighting` 属性也为 `true` 。当 `automaticallyUpdatesLighting` 属性被设为 true ，视图会创建一个或多个 SCNLight 对象，添加到 scene 上，并更新它们的属性，去表现相机场景的预计光线信息。如果你想直接控制 SceneKit 场景中的所有光线，你可以把它设为 false 。

现在，在 `viewDidLoad()` 方法中调用 `configureLighting()` 方法：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    configureLighting()
    addPaperPlane()
}
```
非常棒！在你的设备上编译运行，你会看到一个有漂亮的形状、曲线和边缘的纸飞机！
 
![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p7.png)
 
让我们安静一会儿，爱上纸飞机美丽形状、曲线和边缘。
 
 
### 实现一个多节点的 3D 对象
现在你有一些 3D 模型文件可能包含多个节点。在这样的情况下，让我们看一下如何添加一个多节点的 3D 对象到我们的 ARSCNView 上。
 
在 3D Objects 文件夹下，有一个 *car.dae* 文件，如果你点击它，你将会在 Xcode Scene Editor 中打开这个文件，你可以高亮显示所有节点来查看 3D 汽车对象的轮廓。

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p8.png)

酷！打开 `ViewController.swift` ，在 `addPaperPlane(x:y:z:)` 方法下面插入以下代码：

```swift
func addCar(x: Float = 0, y: Float = 0, z: Float = -0.5) {
    guard let carScene = SCNScene(named: "car.dae") else { return }
    let carNode = SCNNode()
    let carSceneChildNodes = carScene.rootNode.childNodes
        
    for childNode in carSceneChildNodes {
        carNode.addChildNode(childNode)
    }
        
    carNode.position = SCNVector3(x, y, z)
    carNode.scale = SCNVector3(0.5, 0.5, 0.5)
    sceneView.scene.rootNode.addChildNode(carNode)
}
```
这就是我们做的：
* 首先，我们使用 `guard let` 语句给 `car.dae` 文件安全地创建了一个 SCNScene 对象。然后我们为汽车节点初始化了一个 SCNNode 对象。
* 接下来，我们保存了 carScene 根节点的子节点。然后，我们遍历 car scene 的每个子节点，并添加到 car node 上。
* 然后我们简单地将汽车节点的位置设置为给定的参数值，并将汽车节点的 x ， y 和 z 比例值转换为 0.5 以移动其位置。
* 最后，我们把汽车节点添加到 sceneView 场景的根节点上。

现在，在 `viewDidLoad()` 方法中注释 `addPaperPlane()` 方法并调用 `addCar()` 方法：

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    configureLighting()
    //addPaperPlane()
    addCar()
}
```

在你的项目中编译和运行 Xcode 项目，在你们的前面应该会有一辆非常酷的悬浮汽车。

![]({{  site.url  }}/assets/screenshot/arkit-3d-objects/p9.png)

### 结语
恭喜你看完本教程！希望你很享受它。

为了参考，你可以在 [**GitHub 上下载完整的项目**](https://github.com/appcoda/ARKit3DDemo)。

如果你想学习更多关于 ARKit 的知识，可以通过把本教程分享给你的朋友来让我知道。
 