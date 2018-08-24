---
layout: post
title: 「译」ARKit 教程：通过发射一个火箭飞船来理解物理学
date: 2018-08-23 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-physics-scenekit/)    -    原文日期：2018-01-10

只要那样做就可以移动？这是真的吗？那就是增强现实。欢迎回来 ARKit 系列教程的第四部分。在本教程中，我们将会学习一个 ARKit 里面的物理学基础。我们会在教程结尾发射一艘火箭，然后就像 7 月 4 号一样庆祝一下，因为我们可以做到！我们开始吧！

首先，让我们从[**下载启动项目**](downloading the starter project here)开始。编译并运行，你应该会收到一个弹框，提示许在 App 内访问相机。

![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p1.png)

点击 OK 。如果你一切顺利的话，你应该可以看到你的相机视图。

提醒一下，这篇教程是建立在[**上一篇教程**](https://emptywalker.github.io/2018/08/arkit-horizontal-plane/)知识之上的。如果你有很多困惑，你可以随时查看 [**ARKit 系列教程**](https://www.appcoda.com/tag/arkit/)，以便可以在需要时帮助到你。


### Physics Body 解释
首先要做的事情就是解释 physics body 。这是基本知识之一，为了让 SceneKit 知道如何在 app 中模拟一个 SceneKit 节点。我们需要附带上一个 `SCNPhysicsBody` ， `SCNPhysicsBody` 是一个给节点添加物理仿真的对象。

SceneKit 在渲染一个画面之前会在场景中使用附带的 physics bodies 给节点执行**物理计算**。这些计算包括重力、摩擦力和与其它 bodies 的碰撞。你也可以施力去推动一个 body 。这些计算之后，它会更新节点的位置和方向，然后渲染画面。

通常，在每个渲染画面之前，都会进行物理计算。

### Physics Body 类型

为了构建一个 physics body ，你首先需要指定一个 **physics body 类型**。一个 physics body 类型决定了一个 physics body 如何与其它 bodies 的交互动力。三个  physics body 类型分别是静态，动态，和运动。

#### 静态

你可能想给 SceneKit 对象使用一个静态类型的 physics body ，比如地板，墙面，和地形。一个静态类型的 physics body 是不会受外力或者碰撞所影响的，并且不能移动。

#### 动态
你可能想给 SceneKit 对象使用一个动态类型的 physics body ，比如飞行的喷着火的龙，Steph Curry 正在投篮，或发射一艘火箭。动态 physics body 类型是一个会受外力和碰撞影响的 physics body 。

#### 运动的
你可能想使用一个运动的 physics body 表示当你创建一个游戏的时候，你可以使用你的手指去推动一个方块。因此，你创建了一个「无形的」方块推进器，可以通过你手指移动来触发。这个「无形的」方块不会被其它的方块影响，然后，「无形的」方块会移动其它方块，当它们接触时。运动的 physics body 是一个不受外力和碰撞影响的 physics body ，但当它移动的时候回早晨其它 bodies 的碰撞影响，可以移动其它 bodies ，但不会被其它 bodies 移动。


### 创建一个 Physics Body

让我们从给已检测到的水平面一个静态 physics body 开始。在这条路上，我们得有一个坚固的地面，来让我们的火箭站在上面。

在 `ViewController.swift` 的 `renderer(_:didUpdate:for:)` 中添加以下方法：

```swift
func update(_ node: inout SCNNode, withGeometry geometry: SCNGeometry, type: SCNPhysicsBodyType) {
    let shape = SCNPhysicsShape(geometry: geometry, options: nil)
    let physicsBody = SCNPhysicsBody(type: type, shape: shape)
    node.physicsBody = physicsBody
}
```

在这个方法里，我们创建了一个 `SCNPhysicsShape` 对象。 `SCNPhysicsShape` 对象表示 physics body 的外形。当 SceneKit 检测到你的场景中的 `SCNPhysicsBody` 对象相互接触的时候，就会使用你创建的 physics shapes 替换渲染的可视对象的几何体。

接下来，我们创建一个 `SCNPhysicsBody` 对象，给 type 参数传入一个 `.static` 和 把我们的 `SCNPhysicsShape` 对象传给 shape 对象。

然后，我们把 node 的 physics body 设置成我们一起创建的 physics body 。

### 附带一个静态 Physics Body

我们现在去给检测到的平面附带一个静态的 physics body 在 `renderer(_:didAdd:for:)` 方法里面。在添加 `planeNode` 为子节点之前调用下面的方法：

```swift
update(&planeNode, withGeometry: plane, type: .static)
```
添加之后，你的 `renderer(_:didAdd:for:)` 方法，现在看起来应该像这样：

```swift
 func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
        guard let planeAnchor = anchor as? ARPlaneAnchor else { return }
        
        let width = CGFloat(planeAnchor.extent.x)
        let height = CGFloat(planeAnchor.extent.z)
        let plane = SCNPlane(width: width, height: height)
        
        plane.materials.first?.diffuse.contents = UIColor.transparentWhite
        
        var planeNode = SCNNode(geometry: plane)
        
        let x = CGFloat(planeAnchor.center.x)
        let y = CGFloat(planeAnchor.center.y)
        let z = CGFloat(planeAnchor.center.z)
        planeNode.position = SCNVector3(x,y,z)
        planeNode.eulerAngles.x = -.pi / 2
        
        update(&planeNode, withGeometry: plane, type: .static)
        
        node.addChildNode(planeNode)
    }
```
当我们检测到的平面被新的信息更新的时候，它可能会改变几何形状。于是，我们需要在 `render(_:didUpdate:for:)` 中调用相同的方法：

```swift
update(&planeNode, withGeometry: plane, type: .static)
```
`render(_:didUpdate:for:)` 方法在修改后现在看起来应该像这样：

```swift
func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
    guard let planeAnchor = anchor as?  ARPlaneAnchor,
        var planeNode = node.childNodes.first,
        let plane = planeNode.geometry as? SCNPlane
        else { return }
    
    let width = CGFloat(planeAnchor.extent.x)
    let height = CGFloat(planeAnchor.extent.z)
    plane.width = width
    plane.height = height
    
    let x = CGFloat(planeAnchor.center.x)
    let y = CGFloat(planeAnchor.center.y)
    let z = CGFloat(planeAnchor.center.z)
    
    planeNode.position = SCNVector3(x, y, z)
    
    update(&planeNode, withGeometry: plane, type: .static)
}
```

做的漂亮！

### 附带一个动态的 Physics Body

现在，让我们给火箭节点一个动态的 physics body ，因为我们想这个节点可以被外力和碰撞影响。在 `ViewController` 类中声明一个火箭节点名字的常量：

```swift
let rocketshipNodeName = "rocketship"
```
然后在 `addRocketshipToSceneView(withGestureRecognizer:)` 方法里，调整火箭节点的位置之后添加下面的代码：

```swift
let physicsBody = SCNPhysicsBody(type: .dynamic, shape: nil)
rocketshipNode.physicsBody = physicsBody
rocketshipNode.name = rocketshipNodeName
```
我们设置了 `rocketshipNode` 的静态 physics body 和 name 。我们稍等将会看到用 name 去分辨 `rocketshipNode` 。编译运行，你可以看到下面这样的场景：

![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p2.gif)

### 施力

我们现在去给火箭施加外力。

在开始之前，我们需要一个触发这个行为的方式，我们可以使用 `UISwipeGestureRecognizer` 来帮助实现。首先，在 `addRocketshipToSceneView(withGestureRecognizer:)` 方法下面添加以下方法：

```swift
func getRocketshipNode(from swipeLocation: CGPoint) -> SCNNode? {
    let hitTestResults = sceneView.hitTest(swipeLocation)
        
    guard let parentNode = hitTestResults.first?.node.parent,
        parentNode.name == rocketshipNodeName
        else { return nil }
                
    return parentNode
}
```
以上方法将会帮助我们从轻扫手势的轻扫位置获取火箭节点。你可能会想为什么我们要安全解包父节点，原始是返回的节点是来自己碰撞测试的结果，这个结果可能是组成火箭的五个节点之一。
![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p3.png)

在之前方法的正下方，添加以下方法：

```swift
@objc func applyForceToRocketship(withGestureRecognizer recognizer: UIGestureRecognizer) {
    // 1
    guard recognizer.state == .ended else { return }
    // 2
    let swipeLocation = recognizer.location(in: sceneView)
    // 3
    guard let rocketshipNode = getRocketshipNode(from: swipeLocation),
        let physicsBody = rocketshipNode.physicsBody
        else { return }
    // 4
    let direction = SCNVector3(0, 3, 0)
    physicsBody.applyForce(direction, asImpulse: true)
}
```
从上面的代码中，我们可以得到：

1. 确保轻扫手势的状态是 ended 。
2. 从轻扫位置获取碰撞测试结果。
3. 看看轻扫手势是否扫在火箭上。
4. 我们给父节点的 physics body 在 y 轴方向上施加一个力。如果你注意一下，会发现我们也设置了 impulse 参数为 true 。这适用于力量瞬间变化并立即加速 physics body 。通常，当它设为 true 的时候，这个选项允许你去仿真一个瞬时效果，就像发射一个抛射物。

非常棒！编译运行，在火箭上轻扫，你应该会施加一个力在火箭上。
![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p4.gif)

### 添加 SceneKit 的粒子系统并修改 Physics 属性

启动项目中的 「Particles」 文件夹下有一个反应堆 SceneKit 粒子系统。
![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p5.png)
在本教程中，我们不会研究如何去创建一个 SceneKit 粒子系统。我们将会研究如何添加一个 SceneKit 粒子系统到节点上和一些它们的 physics 属性。

打开 `ViewController.swift` 。在 `ViewController` 类中声明以下变量：

```swift
var planeNodes = [SCNNode]()
```

在 `renderer(_:didAdd:for:)` 方法中，把下面的代码作为该方法的最后一行代码：

```swift
planeNodes.append(planeNode)
```

简单的说，当一个新的平面被检测到，我们把它加到我们的平面节点数组中。我们稍后将会为反应堆 SceneKit 粒子系统的对撞机节点属性引用平面节点数组。

在 `renderer(_:didAdd:for:)` 方法正下方，添加实现以下代理方法：

```swift
func renderer(_ renderer: SCNSceneRenderer, didRemove node: SCNNode, for anchor: ARAnchor) {
    guard anchor is ARPlaneAnchor,
        let planeNode = node.childNodes.first
        else { return }
    planeNodes = planeNodes.filter { $0 != planeNode }
}
```

当一个 SceneKit 节点对应的移除 AR 锚已经从场景中移除的时候，这个代理方法就会被调用。这时候，我们只会从平面节点数组中过滤出和被移除的平面节点不相等那些节点。

接下来，在 `applyForceToRocketship(withGestureRecognizer:)` 方法下面添加以下方法：

```swift
@objc func launchRocketship(withGestureRecognizer recognizer: UIGestureRecognizer) {
    // 1
    guard recognizer.state == .ended else { return }
    // 2
    let swipeLocation = recognizer.location(in: sceneView)
    guard let rocketshipNode = getRocketshipNode(from: swipeLocation),
        let physicsBody = rocketshipNode.physicsBody,
        let reactorParticleSystem = SCNParticleSystem(named: "reactor", inDirectory: nil),
        let engineNode = rocketshipNode.childNode(withName: "node2", recursively: false)
        else { return }
    // 3
    physicsBody.isAffectedByGravity = false
    physicsBody.damping = 0
    // 4
    reactorParticleSystem.colliderNodes = planeNodes
    // 5
    engineNode.addParticleSystem(reactorParticleSystem)
    // 6
    let action = SCNAction.moveBy(x: 0, y: 0.3, z: 0, duration: 3)
    action.timingMode = .easeInEaseOut
    rocketshipNode.runAction(action)
}
```

在上面的代码中，我们做了：
1. 确保轻扫手势的状态为 ended 。
2. 像之前一样，安全解包火箭节点和它的 physics body 。同时，我们安全解包了反应堆 SceneKit 粒子系统和引擎节点。我们想把反应堆 SceneKit 粒子系统添加到火箭的引擎上。于是，在引擎节点上就会有有趣的事情了。
3. 设置 physics body 的 *isAffectedByGravity* 属性为 false ，它的效果就像名字一样。引力将不会再影响火箭节点。我们也设置了 damping 属性为 0 ，damping 属性会在 body 上模拟出流体摩擦和空气阻力的效果。设为 0 将会导致在火箭节点的  physics body 上流体摩擦或空气阻力没有效果。
4. 设置反应堆粒子系统去和平面节点碰撞。当它们交互的时候，这将会使粒子系统中的粒子会从已检测到的水平上反弹，，而不是穿过平面节点。
5. 在引擎节点上添加反应堆粒子系统。
6. 我们向上移动火箭节点 0.3 米，伴随着满进慢出的动画效果。


### 添加轻扫手势

在我们可以施力发射火箭之前，我们需要在 scene view 上添加一个轻扫手势，在 `addTapGestureToSceneView()` 下面添加以下代码：

```swift
func addSwipeGesturesToSceneView() {
    let swipeUpGestureRecognizer = UISwipeGestureRecognizer(target: self, action: #selector(ViewController.applyForceToRocketship(withGestureRecognizer:)))
    swipeUpGestureRecognizer.direction = .up
    sceneView.addGestureRecognizer(swipeUpGestureRecognizer)
    
    let swipeDownGestureRecognizer = UISwipeGestureRecognizer(target: self, action: #selector(ViewController.launchRocketship(withGestureRecognizer:)))
    swipeDownGestureRecognizer.direction = .down
    sceneView.addGestureRecognizer(swipeDownGestureRecognizer)
}
```

一个向上轻扫手势将会在火箭节点上施加力量，一个向下的轻扫手势将会发射火箭。漂亮！

最后，但并非最不重要的，在 `viewDidLoad()` 中调用这个方法：

```swift
addSwipeGesturesToSceneView()
```
就这样。


###  Showtime !
恭喜，这是它的表演时间。试试向下轻扫，看看你会得到什么！
![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p6.gif)

继续，试试向下扫然后向上扫，火箭就会发射了！

![]({{  site.url  }}/assets/screenshot/arkit-physics-scenekit/p7.gif)

### 结语
我希望你能享受我的教程并学到一些有价值的知识，随意把这篇文章分享到你的社交网络，让你的圈子也可以获得这些知识。

为了参考，你可以在 [**GitHub 上下载示例项目**](https://github.com/appcoda/ARKitPhysics)。