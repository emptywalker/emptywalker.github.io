---
layout: post
title: 「译」ARKit 教程：保存和恢复 World-mapping 数据来创建持久化的 AR 体验
date: 2018-09-03 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Jayven N](https://medium.com/@jayvenn)    -    [原文地址](https://www.appcoda.com/arkit-persistence/)    -    原文日期：2018-08-17


欢迎来到我们 ARKit 教程系列的第八部分。从 iOS 12 开始， ARKit 就有能力持久化 world-mapping 数据。在之前，你是不能保存 world-mapping 数据。 iOS 12 赋予了开发者创建一个持久化 AR 体验的能力。如果你对在增强现实中构建持久化 world-mapping 数据的 Apps 感兴趣的话，那这篇教程就是为你写的。

下面是你将要构建的内容。

![]({{  site.url  }}/assets/screenshot/arkit-persistence/p1.jpg)

就像你从视频上看到的那样，持久化 world-mapping 数据的意思就是你可以保存 world-mapping 数据并在之后又能恢复 world-mapping 数据，即使 App 被终止。这在 iOS 11 中已经成为一个缺陷了。现在，你可以允许用户通过保存 world-mapping 数据来返回之前的 AR 经历。

### 前提条件

这篇教程需要你对[**我们上一篇讨论的 ARKit 教程里的话题**](https://emptywalker.github.io/2018/09/arkit-image-recognition/)有个深入的理解。如果你是一个 ARKit 新手，请在这里查看[**我们的 ARKit 教程系列**](https://www.appcoda.com/tag/arkit/)。

为了跟着这篇教程，你将需要一个 Xcode 10 测试或更高的版本和跑着 iOS 12 测试或更高版本的 Apple 设备。

不多说了，我们开始。

### 开始
首先，通过在[**这里**](https://github.com/appcoda/ARKit-Persistence-Demo/raw/master/starter.zip)下面启动项目来开始。启动项目已经提前构建了 UI 元素和行为方法。这样，我们就可以关注在 ARKit 的 world-mapping 持久化上了。一旦你下载了启动项目，在你的 iOS 设备上编译运行它。你应该被提示去允许在你的 App 中访问相机。点击 OK 运行在你的 App 访问相机。

![]({{  site.url  }}/assets/screenshot/arkit-persistence/p2.png)

漂亮。现在，我们来讨论一下什么是 `ARWorldMap` ，我们如何使用它实现 ARKit 的world-mapping 数据。

### 使用 ARWorldMap
> 一个 ARWorldMap 对象包含了 ARKit 用于定位用户设备在真实世界的所有空间映射信息的一个快照
> 
 ARWorldMap 对象做的事情和它的名字很像。它是表示在物理世界的一个映射空间。当你使用一个 `ARWorldMap` 的时候，你就有能力去把一个 `ARWorldMap` 对象归档成一个 `Data` 对象，并把它保存到你设备的本地目录下。于是，你就去到你设备中保存世界地图 `Data` 对象的本地目录下解档它。为了恢复映射，你需要将世界跟踪配置的初始世界地图设置为已保存的 `ARWorldMap` 对象。
 
 这就是我们使用 `ARWorldMap` 对象的情况。现在，我们开始实现 demo 。
 
 ### 设置世界地图本地文件目录
 
 首先，声明一个 `URL` 类型的变量，给我们一个可以写入和读取世界地图数据的文件目录路径。在 `ViewController` 类中添加属性：
 
```swift
var worldMapURL: URL = {
    do {
        return try FileManager.default.url(for: .documentDirectory, in: .userDomainMask, appropriateFor: nil, create: true)
            .appendingPathComponent("worldMapURL")
    } catch {
        fatalError("Error getting world map URL from document directory.")
    }
}()
```
将 worldMapURL 放在适当的位置，我们来创建一个世界地图数据归档方法，并把它写入到我们本地文件目录下。



### 将 ARWorldMap 归档成 Data

你现在将去创建一个归档方式用来保存你的 ARWorldMap 对象。在 `ViewController` 类中插入以下代码：

```swift
func archive(worldMap: ARWorldMap) throws {
    let data = try NSKeyedArchiver.archivedData(withRootObject: worldMap, requiringSecureCoding: true)
    try data.write(to: self.worldMapURL, options: [.atomic])
}
```

将 world map 归档成一个 `Data` 对象后，我们把 `data` 对象写入到本地目录下。我们使用 `.atomic` 选项，这是为了保证文件可以在你的设备中完成写入。该方法的签名中有 `throws` 语句，这是由于把数据写入设备文件目录时可能会抛出一个错误，无论是超出存储空间还是其它错误。

随着世界地图归档方法的完成，让我们来归档场景视图中的世界地图。


### 把 AR 世界地图数据保存到你的文档目录中

为了获得当前场景的世界地图， Apple 给了我们一个便利方法去获取。想下面这样更新 `saveBarButtonItemDidTouch(_:)` 方法：

```swift
@IBAction func saveBarButtonItemDidTouch(_ sender: UIBarButtonItem) {
    
    sceneView.session.getCurrentWorldMap { (worldMap, error) in
        guard let worldMap = worldMap else {
            return self.setLabel(text: "Error getting current world map.")
        }
        
        do {
            try self.archive(worldMap: worldMap)
            DispatchQueue.main.async {
                self.setLabel(text: "World map is saved.")
            }
        } catch {
            fatalError("Error saving world map: \(error.localizedDescription)")
        }
    }
}
```

我们的场景视图的会话包含了一个获取当前世界地图的便利方法。在 `getCurrentWorldMap` 闭包里面，我们安全地解包了返回的 `ARWorldMap` 对象。在 `ARWorldMap` 对象不存在的情况下，我们将简单地返回一个带有错误消息的标签文本。

在我们安全地解包了 `ARWorldMap` 对象之后，我们声明了一个 `do-catch` 语句。如果执行 `do` 子句中的代码抛出一个错误，我们将会在 `catch 子句`中处理这个错误。到了这一步，我们停止代码执行，打印出一个错误信息给调试问题用。

编译运行 App 。 扫描你的周围，并点一下你的设备在你的场景中添加一个球。点击 *保存* 按钮，确保你的标签显示 「世界地图已被保存」。现在你当前的 AR 世界地图就被保存了。

### 从你的文件目录中加载 AR 世界地图

随着保存一个 `ARWorldMap` 对象完成了，我们是时候创建一个方法去从文件目录中解档 `ARWorldMap` 数据并把它加载我们的场景中来。

在我们解档世界地图数据之前，我们需要成功的从之前保存的文件目录中获取世界地图数据。

在你的 `ViewController` 类中添加以下方法：

```swift
func retrieveWorldMapData(from url: URL) -> Data? {
    do {
        return try Data(contentsOf: self.worldMapURL)
    } catch {
        self.setLabel(text: "Error retrieving world map data.")
        return nil
    }
}
```

上面的代码声明了一个 do-catch 语句去试图从世界地图的 URL 中检索一个 `Data` 对象。万一代码执行到了 `catch` 子句中，我们就简单地设置一个有错误文本信息的标签。


现在我们有了帮助我们用一个世界地图 URL 从文件目录中获取数据的方法，时候去尝试并将返回的 `Data` 对象解档成一个 `ARWorldMap` 对象。

首先，在你的 `ViewController` 类中添加以下方法：

```swift
func unarchive(worldMapData data: Data) -> ARWorldMap? {
    guard let unarchievedObject = try? NSKeyedUnarchiver.unarchivedObject(ofClass: ARWorldMap.self, from: data),
        let worldMap = unarchievedObject else { return nil }
    return worldMap
}
```

我们在`unarchive(worldMapData:)` 方法里使用 `NSKeyedUnarchiver` 去尝试解档传入的 data 对象。如果解档过程成功并解档的 `ARWorldMap` 对象不为 nil ，我们就返回安全解包的 `ARWorldMap` 对象。

现在我们有了解档方法，我们来用下面的代码更新 `loadBarButtonItemDidTouch(_:)` 方法：

```swift
@IBAction func loadBarButtonItemDidTouch(_ sender: UIBarButtonItem) {
    guard let worldMapData = retrieveWorldMapData(from: worldMapURL),
        let worldMap = unarchive(worldMapData: worldMapData) else { return }
    resetTrackingConfiguration(with: worldMap)
}
```

现在，无论何时我们点击 *加载* 按钮，我们都会调用 `retrieveWorldMapData` 方法去从给定的 URL 中检测世界地图数据。如果检索成功，我们接着就会把世界地图数据解档成一个 `ARWorldMap` 对象。然后，我们带着加载到的数据调用 `resetTrackingConfiguration` 方法去恢复 AR 世界地图。

### 设置场景视图配置的 initialWorldMap 属性

在声明 `options` 常量之后， 把下面的代码添加到 `resetTrackingConfiguration(with:)` ：

```swift
if let worldMap = worldMap {
        configuration.initialWorldMap = worldMap
        setLabel(text: "Found saved world map.")
    } else {
        setLabel(text: "Move camera around to map your surrounding space.")
    }
```
上面的代码设置了场景视图配置的 initialWorldMap 属性为 worldMap 参数。我们然后更新标签的文本表示世界地图被找到。否则，我们就设置标签文本来提醒用户，向周围移动他们的相机去绘制周围空间。

欢聚和演示的时间。

### 演示

这里是 [**YouToBe 视频**](https://youtu.be/t5SObQzl_Pw)。

在演示视频中，用户点击屏幕在场景视图上添加了一个球型节点。然后，用户点击了保存按钮归档了场景视图的当前世界地图。由于归档成功，标签就会更新文本表明「世界地图被保存了」。之后，点击加载按钮，被保存的世界地图就被成功地加载到场景视图中。


很有趣，对不对？

### 结语

恭喜你读到了这里！我希望你能享受并从我的教程中学到一些有价值的事情。请随意把这篇教程分享到你的社交圈，好让你周围的人也可以学到一些知识！


为了参考，你可以在 [**GitHub 上下载完整的 Xcode 项目**](https://github.com/appcoda/ARKit-Persistence-Demo)。