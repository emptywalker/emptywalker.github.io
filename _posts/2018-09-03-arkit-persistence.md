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
