---
layout: post
title: 「译」在 iOS 中集成 Google ML Kit 进行人脸检测、人脸识别等等
date: 2018-08-09 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  [原文地址](https://www.appcoda.com/mlkit/)   
> 
>   原文时间：2018-06-15

就像 Apple 为她的开发者社区做了很多事情一样，另一家不遗余力的为她的开发者创造出色工具和服务的公司就是 Google 。最近几年， Google 已经发布和改进了它的多个服务，以便给 iOS 和 Android 开发者更多的功能，比如 Google Cloud 、Firebase 、 TensorFlow 等等。

今年，在 Google I/O 2018 上， Google 向她的开发者们发布了一款叫做 [**ML Kit**](https://developers.google.com/ml-kit/) 的全新工具包。 Google 一直处于人工智能领域的最前沿，并且通过让开发者使用 ML Kit 模型，Google 已经赋能给了开发者。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p1.png)

使用 ML Kit ，你需要很少的代码就能够执行各种机器学习的任务。 CoreML 和 ML Kit 一个最主要的区别就是：使用 CoreML ，你必须添加自己的模型，但使用 ML Kit ，你可以依靠 Google 为你提供的模型或者使用你自己的模型。在本教程中，我们将依靠使用 Google 的模型，因为添加自己的 ML 模型需要 TensorFlow 和 非常了解 TensorFlow 。

> **编辑提示：**随着 WWDC 18 的发布，你现在可以在 Xcode 10 的 Playgrounds 中使用 [**Create ML 去创建你自己 ML 模型**](https://emptywalker.github.io/2018/08/create-ml/)


另一个不同点就是如果你的模型太大了，你可以把你的 ML 模型放到 Firebase 中，然后让你的 app 去调用服务器。在 CoreML 中，你只能在设备中跑机器学习模型。这有一个 ML Kit 的能力列表：

* 扫描条形码
* 脸部检测
* 图片标签
* 文本识别
* 地表识别
* 智能回复（还没发布，但快了）

在本教程中，我将向你展示如何用 Firebase 创建一个项目，使用 Cocoapods 去下载依赖包和集成 ML Kit 到你的 app ！我们开始！


### 创建一个 Firebase 项目
第一步就是去  [**Firebase 控制台**](https://www.appcoda.com/mlkit/console.firebase.google.com)。在这儿你将会被提示去登录你的 Google 账号。登录之后，你会看到一个欢迎页面，就像这样。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p2.png)

点击 *Add Project* 按钮，然后给你的项目命名，我们这个项目就叫 `ML Kit Introduction` 。保留 Project ID 并根据需求去修改 *国家/地区*，然后点击 *Create Project* 按钮，这可能会还一点时间。

> **注意：** 在到达限额之前你只能创建一定数量的 Firebase 项目，谨慎创建你的项目。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p3.png)

当所有的事情都搞定了之后，你会看到这样的界面！

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p4.png)

这是你的项目概览页面，并且你可以从这个控制台中操作各种各样的 Firebase 控件。恭喜！你刚刚创建了你第一个 Firebase 项目！保持这个项目不变，我们将切换到第二个来看看 iOS 项目。在[**这里**](https://github.com/appcoda/ML-Kit-Demo/raw/master/mlkit-starter.zip)下载启动项目.

### 快速浏览启动项目

打开启动项目，你会看到大部分的 UI 都已经为你设计好了。构建并运行 app ，你会看到一个 `UITableView` ，可以根据 ML Kit 的选项去到不同的页面。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p5.png)

如果你点击了 *Choose Image* 按钮， `UIImagePickerView` 会弹出，选择一张图片就会替换空的占位图。然而，如果没有反应，是因为我们集成了 ML Kit 并且 ML Kit 正在这张图片上执行它的机器学习任务。

### 将 Firebase 链接到 app
回到 Firebase 项目的控制台，点击 「Add Firebase to your iOS App」按钮。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p6.png)

你会收到一个弹框，是关于如何链接 Firebase 的说明。首先要做的就是去链接你输入的 Bundle ID 。你可以在 Xcode 项目概览里面 General 标签下找到它。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p7.png)

把它输入到输入框中，然后点击名为 `Register App` 的按钮，你不需要输入任何信息在选项的文本框中，因为这个 app 不会上传到 App Store 。然后你将会进入第二步，这里你会被要求下载一个 `GoogleService-Info.plist` 文件，这个文件非常重要，将会被添加到你的项目中。点击 `Download` 按钮去下载这个文件。

将文件拖入到项目中，如 Firebase 网站上所示，确保 `Copy items if needed` 选项是被选中的，如果所有事情都做好了，就点击 `Next` 按钮，然后处理第三步。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p8.png)

### 使用 Cocoapods 安装 Firebase 库

下一步将会介绍一下 Cocoapods 的概念。 Cocoapods 对你来说是一个基本方式，可以用一个简单的方法把包导入你的项目中。然后，如果任何细节处理错误，就会导致很多灾难性的后果。首先，关闭 Xcode 的所有窗口并退出。

> **注意：**确保你的设备中已经安装了 Cocoapods 。如果没有的话，这里有个[**很棒的 Cocoapods 教程**](https://www.appcoda.com/cocoapods/)可以教你把它安装到你的 Mac 中。

在 Mac 中打开终端，输入以下命令：

```ssh	
cd  <你的 Xcode Project 路径>
```
> **提示：**为了获取你的 Xcode 项目的路径，选中 Xcode 项目文件，按下 *CMD+C* ，然后，打开终端，输入 *cd* 并粘贴。

你现在已经在那个目录下了。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p9.png)

创建一个 pod ，这很简单，只需要输入下面的命令：

```ssh
pod init
```
等一会儿，你的终端看起来应该像这样，只添加了简单的一行。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p10.png)

现在，让我们把我们所需要的包都添加到我们的 Podfile 中，在终端中输入以下命令，然后等到 Xcode 打开：

```ssh
open -a Xcode podfile
```
![]({{  site.url  }}/assets/screenshot/google-ml-kit/p11.png)

在 `# Pods for ML Kit Starter Project` 下面，输入以下代码：

```ssh
pod 'Firebase/Core'
pod 'Firebase/MLVision'
pod 'Firebase/MLVisionTextModel'
pod 'Firebase/MLVisionFaceModel'
pod 'Firebase/MLVisionBarcodeModel'
pod 'Firebase/MLVision'
pod 'Firebase/MLVisionLabelModel'
```
你的 Podfile 看起来应该像这样。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p12.png)

现在，只剩下一件事情了，回到终端然后输入：

```ssh
pod install
```

这将需要几分钟，因此你可以休息一下，与此同时， Xcode 正在下载我们需要用到的包。当所有出完成后，打开 Xcode 项目文件夹，你将会注意到一个的 .xcworkspace 文件。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p13.png)

这是大部分开发者都很迷惑的地方：你从此以后不可以再打开 .XCODEPROJ 文件了！如果你在那里进行和编辑内容，那么这两个内容将不会同步，你将不得不重新创建一个新项目。从现在起，你应该一直打开 .xcworkspace 文件。

回到 Firebase 网页，你将会注意到我们已经完成了第三步，点击 NEXT 按钮，进入第四步。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p14.png)

我们现在需要去打开我们的 workspace ，然后在 `AppDelegate.swift` 文件添加一些代码。打开 .xcworkspace （再次说明，不是 .xcodeproj 文件，我无法重复这是多么重要），然后进入 `AppDelegate.swift` 文件。

一旦我们进入 `AppDelegate.swift` ，我们要做的所有事情就是添加两行代码。

```swift
import UIKit
import Firebase
 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
 
    var window: UIWindow?
 
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // 应用启动后重写 FirebaseApp 的自定义配置
        FirebaseApp.configure()
        return true
    }
```
我们正在做的所有事情就是导入 Firebase 包，然后基于我们之前添加的 `GoogleService-Info.plist` 文件去配置它。你可以会得到一个 Firebase 模块编译不过的错误，只需要按需 CMD+SHFT+K 去清理一下项目，然后 CMD+B 构建一下。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p15.png)

如果还有错误，打开项目的 `Build Settings` 栏，然后搜索 `Bitcode` 。你将会在 `Build Options` 下面看到一个 `Enable Bitcode` 的选项，把它设置成 NO ，重新构建。你现在应该会成功！

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p16.png)

在 Firebase 控制台上点击 `NEXT` 按钮，进入第5步。现在，你所需要做的事情就是在一个设备上运行你的 app ，第5步将会被自动完成。然后你将会被重定向回到项目概览页面，现在这里会向你展示一些数据。

![]({{  site.url  }}/assets/screenshot/google-ml-kit/p17.png)

恭喜！你已经完成了本教程中最富有挑战的部分，剩下的就是用 Swift 添加 ML Kit 代码。现在是休息的最佳时机，但从现在开始，它只是在我们熟悉的代码中平稳的运行！