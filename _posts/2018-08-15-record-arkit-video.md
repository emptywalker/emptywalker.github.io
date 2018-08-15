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

### 添加 UI

我们将以编程的形式构建 UI ，而不是使用 Interface Builder 。为了保持简单，我们将创建 3 个简单的按钮：录制/停止、暂停/继续、和捕获 GIFs 。

为了完成这些，在 `ViewController` 类中声明以下属性：

```swift
// Recorder UIButton. 这个按钮将会开始和停止视频的录制。
var recorderButton:UIButton = {
    let btn = UIButton(type: .system)
    btn.setTitle("Record", for: .normal)
    btn.setTitleColor(.black, for: .normal)
    btn.backgroundColor = .white
    btn.frame = CGRect(x: 0, y: 0, width: 110, height: 60)
    btn.center = CGPoint(x: UIScreen.main.bounds.width/2, y: UIScreen.main.bounds.height*0.90)
    btn.layer.cornerRadius = btn.bounds.height/2
    btn.tag = 0
    return btn
}()
 
// Pause UIButton. 这个按钮将会暂停一个视频的录制。
var pauseButton:UIButton = {
    let btn = UIButton(type: .system)
    btn.setTitle("Pause", for: .normal)
    btn.setTitleColor(.black, for: .normal)
    btn.backgroundColor = .white
    btn.frame = CGRect(x: 0, y: 0, width: 60, height: 60)
    btn.center = CGPoint(x: UIScreen.main.bounds.width*0.15, y: UIScreen.main.bounds.height*0.90)
    btn.layer.cornerRadius = btn.bounds.height/2
    btn.alpha = 0.3
    btn.isEnabled = false
    return btn
}()
 
// GIF UIButton. 这个按钮将会捕获 GIF 图片。
var gifButton:UIButton = {
    let btn = UIButton(type: .system)
    btn.setTitle("GIF", for: .normal)
    btn.setTitleColor(.black, for: .normal)
    btn.backgroundColor = .white
    btn.frame = CGRect(x: 0, y: 0, width: 60, height: 60)
    btn.center = CGPoint(x: UIScreen.main.bounds.width*0.85, y: UIScreen.main.bounds.height*0.90)
    btn.layer.cornerRadius = btn.bounds.height/2
    return btn
}()
```
接下来，把这些按钮添加到  View Controller 的子视图上。在 `viewDidLoad()` 方法中插入下面几行代码：

```swift
self.view.addSubview(recorderButton)
self.view.addSubview(pauseButton)
self.view.addSubview(gifButton)
```
为了处理按钮的行为，我们会创建 3 行为方法：

```swift
// 录制和停止方法
@objc func recorderAction(sender:UIButton) {
        
}
// 暂停和继续方法
@objc func pauseAction(sender:UIButton) {
    
}
// 捕获 GIF 方法
@objc func gifAction(sender:UIButton) {
 
}
```
现在，回到 `viewDidLoad()` ，添加按钮的目标，并把他们和上面创建的方法连接起来：

```swift
recorderButton.addTarget(self, action: #selector(recorderAction(sender:)), for: .touchUpInside)
pauseButton.addTarget(self, action: #selector(pauseAction(sender:)), for: .touchUpInside)
gifButton.addTarget(self, action: #selector(gifAction(sender:)), for: .touchUpInside)
```
在我们开始下一部分之前，让我们运行这个应用看看到目前为止我们已经构建了什么。如果你跟着我做的没有出错地话，你将会有一个简单的在屏幕上有三个按钮的 ARKit app 。记住你必须在真实设备上测试 app 而不是在模拟器上。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p6.jpg)

### 实现 ARVideoKit 框架

现在，是时候开启录制功能了！我们将会在 `ViewController.swift` 中实现 `ARVideoKit` 框架。每个第一步都是导入框架：

```swift
import ARVideoKit
```

接下来，创建一个 RecordAR 类型的变量。 `RecordAR` 是 ARView 的一个子类，使用设备相机流渲染一个 ARSCNView 或 ARSKView 的内容，去生成一个视频，图片， live 图片 或者 GIF 。

```swift
var  recorder: RecordAR?
```

在 `viewDidLoad()` 方法中，用 ARSKView 初始化 RecordAR ，并指定支持的方向：

```swift
// 用 SpriteKit 场景初始化
recorder = RecordAR(ARSpriteKit: sceneView)
 
// 指定支持的方向
recorder?.inputViewOrientations = [.portrait, .landscapeLeft, .landscapeRight]
```

为了准备录制，在 `viewWillAppear(_ animated: Bool)` 方法中插入以下语句：

```swift
recorder ?.prepare(configuration)
```
最后，当视图消失的时候，去「休息」录制器。在 `viewWillDisappear(_ animated: Bool)` 方法中插入下面一行代码：

```swift
recorder?.rest()
```

### 开发录制/停止和暂停/继续函数
现在， `RecordAR` 变量已经准备好了，让我们转到实现录制和停止的功能上。

对于录制行为方法，更新方法想下面这样：

```swift
@objc func recorderAction(sender:UIButton) {
    
    if recorder?.status == .readyToRecord {
        // 开始录制
        recorder?.record()
        
        // 修改按钮标题
        sender.setTitle("Stop", for: .normal)
        sender.setTitleColor(.red, for: .normal)
        
        // 暂停按钮可用
        pauseButton.alpha = 1
        pauseButton.isEnabled = true
        
        // 是 GIF 按钮不可用
        gifButton.alpha = 0.3
        gifButton.isEnabled = false
    }else if recorder?.status == .recording || recorder?.status == .paused {
        // 停止录制并从相机卷中导出视频
        recorder?.stopAndExport()
        
        // 改变按钮标题
        sender.setTitle("Record", for: .normal)
        sender.setTitleColor(.black, for: .normal)
        
        // 使 GIF 按钮可用
        gifButton.alpha = 1
        gifButton.isEnabled = true
        
        // 使暂停按钮不可用
        pauseButton.alpha = 0.3
        pauseButton.isEnabled = false
    }
}
```
在上面这段代码中，我们会检查视频录制器的状态如果是*准备录制*，应用程序就会把你的 ARKit 场景录制成一个视频。否则，如果录制器当前的状态是*录制中*或*已暂停*，应用程序就会停止视频录制器，并从相机卷中导出全部渲染后的视频。

接下来，我们将会在 `pauseAction(sender:UIButton)` 方法中实现暂停/继续功能，更新 `pauseAction` 方法，向下面这样：

```swift
@objc func pauseAction(sender:UIButton) {
    if recorder?.status == .recording {
        // 暂停录制
        recorder?.pause()
        
        // 改变按钮标题
        sender.setTitle("Resume", for: .normal)
        sender.setTitleColor(.blue, for: .normal)
    } else if recorder?.status == .paused {
        // 继续录制
        recorder?.record()
        
        // 改变按钮标题
        sender.setTitle("Pause", for: .normal)
        sender.setTitleColor(.black, for: .normal)
    }
}
```
上面一段代码非常直接。我们首先检查如果录制器当前处于*录制中*状态，当用户点击暂停按钮时应用程序就会暂停视频录制。否则，就继续录制。

现在，我们需要测试一下！在你的 iOS 设置运行应用程序之前，我们需要确保已经在 app 的 `Info.plist` 文件中添加了 `camera` 、 `microphone` 和 `photo library` 的使用描述。

为了完成这个，把下面的代码添加到 plist 源码中：

```
<key>NSCameraUsageDescription</key>
<string>AR Camera</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>Export AR Media</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>Export AR Media</string>
<key>NSMicrophoneUsageDescription</key>
<string>Audiovisual Recording</string>
```

或者，你可以选择 Property Editor 添加这些属性：

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p7.png)

现在让我们来运行它！📲🎊 点击录制按钮，开始录制你的 AR 视频。

<iframe 
    width="800" 
    height="450" 
    src="https://youtu.be/PHziPwFtdyo"
    frameborder="0" 
    allowfullscreen>
</iframe>

### 捕获 GIFs

最后，让我们实现 GIFs 函数，以便你可以捕获动画的  GIFs 。更新 `gifAction` 方法，像这样：

```swift
@objc func gifAction(sender:UIButton) {
        self.gifButton.isEnabled = false
        self.gifButton.alpha = 0.3
        self.recorderButton.isEnabled = false
        self.recorderButton.alpha = 0.3
 
        recorder?.gif(forDuration: 1.5, export: true) { _, _, _ , exported in
            if exported {
                DispatchQueue.main.sync {
                    self.gifButton.isEnabled = true
                    self.gifButton.alpha = 1.0
                    self.recorderButton.isEnabled = true
                    self.recorderButton.alpha = 1.0
                }
            }
        }
    }
```

### 修改 SpriteKit 内容

在这最后的一部分，我们将会修改 SpriteKit 的内容去在 AR 空间中展示一些不同的 emojis 🤗🤓🧐🔥 。

首先我们创建一个变量，可以从*一个 emojis 的数组*中返回一个*随机的 emoji* 。通过使用基于 C 语言的函数 [**`arc4random_uniform()`**](https://developer.apple.com/library/archive/documentation/Darwin/Reference/ManPages/man3/arc4random_uniform.3.html) ，我们可以检索到一个在 0 至数组个数之间的随机数。

为了完成这个，我们在 `ViewController` 类中创建以下变量作为全局变量（在 `gifButton` 之后替换它）：

```swift
var randoMoji: String {
    let emojis = ["👾", "🤓", "🔥", "😜", "😇", "🤣", "🤗", "🧐", "🛰", "🚀"]
    return emojis[Int(arc4random_uniform(UInt32(emojis.count)))]
}
```

接下来把 `view(_ view: ARSKView, nodeFor anchor: ARAnchor) -> SKNode?` 方法编辑成这样：

```swift
func view(_ view: ARSKView, nodeFor anchor: ARAnchor) -> SKNode? {
    // 为添加到视图会话的锚点创建和添加一个配置
    let labelNode = SKLabelNode(text: randoMoji)
    labelNode.horizontalAlignmentMode = .center
    labelNode.verticalAlignmentMode = .center
    return labelNode;
}
```

我们只是使用了新创建的 `randoMoji` 替换了 `SKLabelNode` 的静态文本。

### 就这样！玩的开心

你现在可以在你的设备中运行你的应用程序，然后使用它去录制 ARKit 视频和GIFs!想要下载完整的项目，你可以在 [**GitHub 中下载**](https://github.com/AFathi/record-arkit-demo)。

![]({{  site.url  }}/assets/screenshot/record-arkit-video/p8.gif)

你认为这篇教程怎么样？请在下面给我留言并分享你的想法。