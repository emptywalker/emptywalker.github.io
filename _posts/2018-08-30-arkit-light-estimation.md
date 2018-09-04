---
layout: post
title: 「译」ARKit 教程：具有环境强度和色温的关照估计
date: 2018-08-30 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-light-estimation/)    -    原文日期：2018-02-18


曾几何时，岩石打滑，发出火花，人类学会了生火。这是我们 ARKit 系列的第五部分。今天，我们将学习如何使用 ARKit 在增强现实中实现光照估计。

光照估计是在 AR 中强化你的图像和真实世界的融合 —— 利用着色算法。当你的应用渲染图像时，你可以使用渲染信息和着色算法将相机捕获到的真实世界的光照条件与场景图像相匹配。

我希望你会享受这篇 ARKit 教程。同时希望，这边教程也可以撞击出一个疯狂的想法，就像火箭一样。


现在，让我们开始。

### 前提条件

这篇 ARKit 教程是建立在[**前一篇 ARKit 教程**](https://emptywalker.github.io/2018/08/arkit-physics-scenekit/)的基础上。如果你有任何困惑，你可以随时查看 [**ARKit 系列教程**](https://www.appcoda.com/tag/arkit/)，以便可以在需要时帮助到你。


### 你将要实现和学习的内容

在创建本篇教程的 ARKit 光照估计项目后，我们将会做到以下内容：

* 在一个已检测的水平面上放一个球体节点
* 使用一个光照节点演示球体节点
* 测试光照强度和温度属性
* 更新和实现 UI
* 最后，在一个 SceneKit 的场景渲染方法中实现光照估计。


### 开始

首先，[**下载启动项目**](https://github.com/appcoda/ARKitLightEstimationDemo/raw/master/ARKitLightEstimationStarter.zip)。我已经构建了应用的 UI 和创建的按钮的行为方法。


![]({{  site.url  }}/assets/screenshot/arkit-light-estimation/p1.png)

编译运行。你应该被提示是否允许在 App 里面访问相机。点击 OK 允许在你的 App 中访问相机。

![]({{  site.url  }}/assets/screenshot/arkit-light-estimation/p2.png)

### 创建一个球体节点

首先，我们将通过在增强现实中创建一个球开始。在 Xcode 中打开 `ViewController.swift` 文件。在 `ViewController` 类中添加以下代码：

```swift
func getSphereNode(withPosition position: SCNVector3) -> SCNNode {
    let sphere = SCNSphere(radius: 0.1)
    
    let sphereNode = SCNNode(geometry: sphere)
    sphereNode.position = position
    sphereNode.position.y += Float(sphere.radius) + 1
    
    return sphereNode
}
```
`getSphereNode(withPosition:)` 方法做了以下事情：

* 传入一个 position 参数
* 创建一个半径为 0.1 CGFloat 的球形几何体
* 使用我们前面创建的球形几何体创建一个球形节点
* 将球型节点的 position 设置成 position 参数的值
* 给球型节点的 y 位置的值加上球体半径值的值，使用球体在检测到的水平面的正上方
* 给球型节点 y 位置增加 1 。这样，球型节点将会位于检测到的平面上方 1 米处
* 返回球型节点

简而言之，这个方法创建了一个球型节点，并把它放到一个检测到的平面的上方。

### 添加一个光照节点

接下来，我们将添加一个光照源去照亮场景。在 `ViewController` 类中创建下面的方法：

```swift
func getLightNode() -> SCNNode {
    let light = SCNLight()
    light.type = .omni
    light.intensity = 0
    light.temperature = 0
    
    let lightNode = SCNNode()
    lightNode.light = light
    lightNode.position = SCNVector3(0,1,0)
    
    return lightNode
}
```
照亮场景的方法是使用 light 属性将灯光附加到 SCNNode 对象上。这就是上面方法做的事情。让我来仔细说解释一下 `getLightNode()` 方法做了什么：
* 首先，我们创建了一个 SceneKit 光照对象（例如：`SCNLight` ），并把它的 type 属性设为 omni 。一个全方位的光照类型是从一个点到所有方向照亮一个场景。其它的光照类型包括定向，点和周围。
* 接下来，我们把 light 对象的 intensity 和 temperature 属性值设为 0 。
* 为了使 light 对象可以照亮场景，我们创建了一个光照节点并设置节点的光源属性为 `light` 。
* 我们也设置光照节点对象的 y 位置在它的父节点正上方一米处。

现在，让我们在 `ViewController` 类里面添加另一个方法：

```swift
func addLightNodeTo(_ node: SCNNode) {
    let lightNode = getLightNode()
    node.addChildNode(lightNode)
    lightNodes.append(lightNode)
}
```
这个方法调用 `getLightNode()` 方法获取一个光照节点，并把光照节点添加到给定的节点上。同时，我们把光照节点添加到光照节点数组中。


接下来，在 `planeAnchorCenter` 常量正下方的 `renderer(_:didAdd:for:)` 方法里面添加以下内容：

```swift
let sphereNode = getSphereNode(withPosition: planeAnchorCenter)
addLightNodeTo(sphereNode)
node.addChildNode(sphereNode)
detectedHorizontalPlane = true
```

上面的代码做的事情：
* 用平面锚点中心位置获取一个球型节点
* 在球型节点上添加一个光照节点
* 设置映射锚节点作为球型节点的父节点
* 设置检测到的水平面的可视化为 ture 


### 测试光照属性

现在，让我测试一下环境强度和色温对已渲染的图像的影响。开始之前，像这样更新一下 `ambientIntensitySliderValueDidChange(_:)` 方法：

```swift
@IBAction func ambientIntensitySliderValueDidChange(_ sender: UISlider) {
    DispatchQueue.main.async {
        let ambientIntensity = sender.value
        self.ambientIntensityLabel.text = "Ambient Intensity: \(ambientIntensity)"
        
        guard !self.lightEstimationSwitch.isOn else { return }
        for lightNode in self.lightNodes {
            guard let light = lightNode.light else { continue }
            light.intensity = CGFloat(ambientIntensity)
        }
    }
}
```
上面的代码运行在主线程，并把光照节点的光照强度属性值设为 slider 的值，像这样更新 `ambientColorTemperatureSliderValueDidChange(_:)`
 方法：
 
```swift
@IBAction func ambientColorTemperatureSliderValueDidChange(_ sender: UISlider) {
    DispatchQueue.main.async {
        let ambientColorTemperature = self.ambientColorTemperatureSlider.value
        self.ambientColorTemperatureLabel.text = "Ambient Color Temperature: \(ambientColorTemperature)"
        
        guard !self.lightEstimationSwitch.isOn else { return }
        for lightNode in self.lightNodes {
            guard let light = lightNode.light else { continue }
            light.temperature = CGFloat(ambientColorTemperature)
        }
    }
}
```
上面的代码运行在主线程，并把光照节点的温度属性值设为 slider 的值，

库！我们编译运行这个项目。把设备的相机对着一个水平面。根据水平面检测，你应该会看到一个悬浮球。随意滑动 sliders 去修改光照强度和色温属性。

![]({{  site.url  }}/assets/screenshot/arkit-light-estimation/p3.gif)

### 显示/隐藏光照估计开关

现在， 环境强度和色温控制都是显示的。但对于光照估计开关，默认是隐藏的。我想在检测到一个水平面之后再显示控制器。因此，像这样更新 `detectedHorizontalPlane` 属性的 `didSet` 方法：

```swift
var detectedHorizontalPlane = false {
    didSet {
        DispatchQueue.main.async {
            self.mainStackView.isHidden = !self.detectedHorizontalPlane
            self.instructionLabel.isHidden = self.detectedHorizontalPlane
            self.lightEstimationStackView.isHidden = !self.detectedHorizontalPlane
        }
    }
}
```
这个 lightEstimationStackView 包含了一个 UISwitch 对象和一个 UILabel 对象。当设置 `detectedHorizontalPlane` 被设置为 `true` 的时候，我们显示 lightEstimationStackView 。

### 实现光照估计开关

现在我们将去实现光照估计开关。在 `lightEstimationSwitchValueDidChange(_:)` 方法里添加以下代码：

```swift
ambientIntensitySliderValueDidChange(ambientIntensitySlider)
ambientColorTemperatureSliderValueDidChange(ambientColorTemperatureSlider)
```

一旦光照估计开关值改变了，我们就更新光照节点 light 的 intensity 和 temperature 属性成它们各自 slider 的值。







