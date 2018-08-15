---
layout: post
title: 「译」使用 ARVideoKit 记录视频 & 动画 GIFs
date: 2018-08-09 17:26:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Ahmed Fathi Bekhit](https://github.com/AFathi)    -    [原文地址](https://www.appcoda.com/record-arkit-video/)    -    原文日期：2017-11-29


最近，移动增强现实已经成为一种趋势，它已经应用在很多应用程序中，用于给人们提供更丰富的交互，分享有趣的体验。因此，主流的增强现实框架可以让开发者很简单地在他们的 apps 中实现复杂的计算机视觉功能，并在移动端上制造一个 **AR** 现实 😉 ，比如 [**ARKit**](https://emptywalker.github.io/2018/08/simple-arkit-demo/) 。

然后， ARKit和类似的框架，并没有简化使用增强现实组件录制视频的过程，尽管用户**热衷**于在使用增强现实时分享他们的有趣体验（参考面部过滤和像 AR 热狗这样的趋势），这导致了开发者去使用录屏或截图等替代解决方案。

在本教程中，我们将演示不使用*录屏或截图*如何去录制包含增强现实组件的**视频**和**GIFs**。为了实现这个，我们使用一个特别构建的框架去简化渲染和录制 ARKit 视频的过程，叫做 [**ARVideoKit**](https://github.com/AFathi/ARVideoKit) 。这个框架是使用 Apple 的 [**ARKit**](https://developer.apple.com/documentation/arkit) ， [**AVFoundation**](https://developer.apple.com/documentation/avfoundation) ， [**Metal**](https://developer.apple.com/documentation/metal) 和 [**CoreMedia**](https://developer.apple.com/documentation/coremedia) 构建的，用于减掉使用相机流渲染一个 [**ARSCNView**](https://developer.apple.com/documentation/arkit/arscnview) 或 [**ARSKView**](https://developer.apple.com/documentation/arkit/arskview) 内容过程中复杂的部分。

### 开始！

开始，我们首先打开[**仓库的 release 页面**](https://github.com/AFathi/ARVideoKit/releases)，下载最近发布的 [**ARVideoKit**](https://github.com/AFathi/ARVideoKit/releases) ，然后下载 `Framework.zip` 文件。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p1.png)

然后我们将在 Xcode 里创建一个新的 `ARKit` 项目。对于本教程，我们创建一个 `ARKit` + `SpriteKit` 项目。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p2.png)

当项目创建好之后， Xcode 将会自动生成一份 SpriteKit 的示例代码给我们使用。尽管，这样对 demo 很有用，但我们将会对它进行一点修改，添加我们自己的用户界面，视频录制器， GIF 制作器，然后显示更多不同的 emojis 😇 ！

### 添加框架

我们将从把 `ARVideoKit` 添加到项目中开始！为了正确地添加框架，需要做以下几步：

1. 在你的项目文件夹中创建一个 Frameworks 文件夹。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p3.png)

2. 把下载的 ARVideoKit.framework 拷贝到 *Frameworks* 文件夹中

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p4.gif)

3. 把 ARVideoKit.framework 拖入项目目标的 Embedded Binaries ，确保 「Copy items if needed」勾选上了。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p5.png)

现在，让我们在 application delegate 中配置框架，采取以下步骤：
1. 通过添加下面的语句在 `AppDelegate.swift` 中导入 `ARVideoKit`

```swift
import ARVideoKit
```
2. 在 `AppDelegate.swift` 类中添加下面的方法，去允许在不同的方向录制视频 & GIFs 。

```swift
func application(_ application: UIApplication, supportedInterfaceOrientationsFor window: UIWindow?) -> UIInterfaceOrientationMask {
    return ViewAR.orientation
}
```

你的应用程序代理文件看起来应该像这样：

```swift
import UIKit
import ARVideoKit
 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
 
    var window: UIWindow?
 
 
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }
 
    func applicationWillResignActive(_ application: UIApplication) {
        // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
        // Use this method to pause ongoing tasks, disable timers, and invalidate graphics rendering callbacks. Games should use this method to pause the game.
    }
 
    func applicationDidEnterBackground(_ application: UIApplication) {
        // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
        // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
    }
 
    func applicationWillEnterForeground(_ application: UIApplication) {
        // Called as part of the transition from the background to the active state; here you can undo many of the changes made on entering the background.
    }
 
    func applicationDidBecomeActive(_ application: UIApplication) {
        // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
    }
 
    func applicationWillTerminate(_ application: UIApplication) {
        // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
    }
 
    
    func application(_ application: UIApplication, supportedInterfaceOrientationsFor window: UIWindow?) -> UIInterfaceOrientationMask {
        return ViewAR.orientation
    }
 
}
```






