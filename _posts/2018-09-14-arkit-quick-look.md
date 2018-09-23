---
layout: post
title: 「译」介绍 iOS 12 中的 AR Quick Look 
date: 2018-09-14 11:06:24.000000000 +09:00
---

> 本文是翻译于AppCoda社区，如有版权问题，请告知，会配合处理
>  
>  作者：[Sai Kambampati](https://www.appcoda.com/author/saikambampati/)    -    [原文地址](https://www.appcoda.com/arkit-quick-look/)    -    原文日期：2018-07-18

在 WWDC 2018 上， Apple 发布了 ARKit 2.0 ，给增强现实开发带来了一系列全新的 APIs 和功能。其中一个功能就是对他们的 Quick Look APIs 的补充。如果你不熟悉 Quick Look 是什么，它是一个基础框架，允许用户预览整个文件，格式如 PDFs ， 图片等！例如 iOS 中的邮件应用使用 Quick Look 来预览附件。

在你的应用中使用 Quick Look 的一个优点就是你只需要声明你可能会快速浏览的是什么文件。框架会处理显示的 UI 和 UX 使得它很容易集成。在继续之前，我建议你看一下[**这篇**](https://www.appcoda.com/quick-look-framework/)关于使用 Quick Look 框架预览文档的教程。

今年，对于 iOS 12 ， Apple 已经介绍了针对增强现实对象的 Quick Look 。这意味着你可以用 Mail ， Messages 或其它支持这种类型的 Quick Look 的应用程序来分享 `.usdz` （稍后详细介绍）文件。接受器不需要下载额外的 App 就可以打开它并查看对象。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p1.jpg)

在上面的图片里，你可以看到在不需要其它的 App 帮助下一个茶壶在 AR 上被预览。 然而，AR Quick Look 不仅仅局限在 App 中使用，你也可以集成 AR Quick Look 到网站中！在这篇教程中，我将会陪你学习在一个 iOS 应用程序中集成 AR Quick Look ，同时，使用 GitHub Pages 构建一个非常基础的 HTML 网站来学习我们如何在网站中添加 AR Quick Look ！只需要在任何运行 iOS 12 的设备上查看 Apple 的 [**AR 图库**](https://developer.apple.com/arkit/gallery/)就可以了！

> **提示：**对于本篇教程，我们将会使用在写作的时候任然处于测试版本的 Xcode 10 。为了看到真正地 AR Quick Look 效果，你需要将 App 运行在跑着 iOS 12 的设备上。在你的 App 开发从始至终都记住这一点。
> 

### 什么是 USDZ ?

在我们开始编码之前，理解什么是 USDZ 非常重要。和很多文件格式一样， USDZ 也是其中之一。它代表**通用场景描述压缩**( Universal Scene Description Zip )。如果你之前接触过 3D 模型，你可能会对 3D 模型比较熟悉，像 `.OBJ` , `.DEA` 或 `.sketchup` 。 USDZ 是由皮克斯（ Pixar ）和 Apple 合作创建一种文件。

其核心，一个 USDZ 文件只不过是 `.zip` 的归档文件，就是将模型和纹理打包成一个单文件。这就是为什么 USDZ 文件被用于 Quick Look 而不是其它 3D 模型格式。

现在你可能会思考，「我该如何创建一个 USDZ 文件？」好的，它实现的方式就是使用你最喜爱的 3D 模型软件( AutoCAD, Blender, Maya, etc 等等)创建你自己的 3D 模型，并使用 Xcode 命令行工具去把这个文件转换成一个 `.usdz` 文件格式。

让我们尝试把我们自己的模型转换成 USDZ 文件格式！

### 把 3D 模型转换成 USDZ 文件格式
一个模型转换成 USDZ 非常简单，只需要一行代码！我们将转换我创建的一颗蛋的 3D 对象模型。你可以在[**这里**](https://github.com/appcoda/AR-Quick-Look-Demo/raw/master/egg.obj)下载这个模型。

当你下载模型，你会看到它仅仅是一个蛋。现在，我们尝试将模型转换成一个 USDZ 文件格式。打开终端，输入下面一行：

```swift
xcrun usdz_converter /Users/You/PATH/TO/egg.obj /Users/You/CHOOSE/A/PATH/TO/SAVE/egg.usdz
```

就这样！下面就是我的终端的样子：
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p2.png)

按下回车键，等待几秒，你将会看到 `.usdz` 文件保存你想保存的路径下。按下空格键快速查看一下文件。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p3.png)

如果是黑的不用担心。我不确定这是否是 Apple 那边的问题或者是否他们的意思就是这样。无论如何，当你预览它的时候，所有的颜色都会被恢复。

就这样！现在我们有了自己的 `USDZ` 文件，让我们开始把它集成到我们的项目中。

### 在你的 Apps 中添加 AR Quick Look

我们通过在[**这里**](https://github.com/appcoda/AR-Quick-Look-Demo/raw/master/ARQuickLookStarter.zip)下载启动项目来开始。看一下项目，你会看到已经放了一个 collection view 。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p4.png)

运行 App 。你将会看到一个有一群模型的列表，但当你点击它们的时候，没有任何反应。这是由我们决定的，确保用户可以快速查看模型！
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p5.png)

首先，让我们把 Egg 模型添加到 `Models` 文件夹。把 `egg.usdz` 拖到 models 文件夹。确保当你把它拖进文件里的时候，你像下面一样选中 target box。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p6.png)

接下来，打开 `ViewController.swift` ，把 `egg` 添加到 `models` 数组。这样当我们运行 App 时，model 将会展现在列表中。为了确定，再运行一次 App 。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p7.png)

这就 OK 了！现在就剩下添加代码来快速浏览这些模型了。首先，从导入 `QuickLook` 包开始。当我们创建 `UICollectionView` 的时候，我们也添加了 DataSource 和 Delegate 协议，为了给我们需要给 collectionView 添加数据的时候访问这些方法。同样的，我们也为 Quick Look 做同样的事情。像下面这样修改你的代码：

```swift
import UIKit
import Foundation
import QuickLook
 
class ViewController: UIViewController, UICollectionViewDelegate, UICollectionViewDataSource, QLPreviewControllerDelegate, QLPreviewControllerDataSource
```
有两个方法需要我们添加为了配置我们添加的协议：`numberOfPreviewItems()` 和 ` previewController(previewItemAt)` 。如果你用过 `UITableView` 或者 `UICollectionViewx` 那你对这个应该会比较熟悉。在 `collectionView(didSelectItemAt)` 下面的类的底部添加以下代码：

```swift
func numberOfPreviewItems(in controller: QLPreviewController) -> Int {
    return 1
}
    
func previewController(_ controller: QLPreviewController, previewItemAt index: Int) -> QLPreviewItem {
    let url = Bundle.main.url(forResource: models[thumbnailIndex], withExtension: "usdz")!
    return url as QLPreviewItem
}
```

1. 在第一个方法中，我们确定同时能有多少个 items 可以被允许预览。由于我们一次只想预览一个 3D 模型，我们就在代码中返回 1 。
2. 第二个方法确定当一个 item 在特定的 `index` 上被点击的时候，哪一个文件应该被预览。我们定义了一个叫做 `url` 的常量用来存放 `.usdz` 文件的路径。然后，我们返回 `QLPreviewItem` 类型的文件。

> **注意：** 注意我们是如何使用 *thumbnailIndex* 去指定我们使用的模型。当用户点击 collectionView cell 的时候，我们在 *collectionView(didSelectItemAt)* 方法中设置 *thumbnailIndex* 的数字作为处理依据。
> 
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p8.png)

如果你现在运行代码，什么都不会发生。为什么呢？这是因为我们还没有添加逻辑代码去展示 `QLPreviewController` 。回到 `collectionView(didSelectItemAt)` 方法，并像下面这样修改代码：

```swift
func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
    thumbnailIndex = indexPath.item
 
    let previewController = QLPreviewController()
    previewController.dataSource = self
    previewController.delegate = self
    present(previewController, animated: true)
}
```
就像前面提到的一样，我们把 `thumbnailIndex` 的值设为用户点击的 `index`，这会帮助 Quick Look Data Source 方法知道我们使用的模型是什么。如果你在你的 apps 里面使用 Quick Look 支持所有文件类型，你将总会在 `QLPreviewController` 中展示。不论将要展示的是一个文件，一张图片，或者在本例中，一个 3D 模型， `QuickLook` 框架都需要你用一个 `QLPreviewController` 来展示它们。我们把 `previewController` 的 data source 和 delegate 都设置成 `self` 并展示它！

 Quick Look 的所有代码，应该像这样：
 
 ![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p9.png)
 
 编译运行你的 App 。确保 App 运行在一个运行 iOS 12 的真实设备上。运行在模拟器上将不会展示出 Quick Look 预览。
 
  ![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p10.png)
 
 它到达了预期的效果了！你现在应该知道如何在你的 Apps 中去集成 AR Quick Look 。但这并不是全部，因为 AR Quick Look 还提供了网页支持！在下一部分，我将会指导你使用 HTML 和 AR Quick Look 构建一个网站。
 
### 用 HTML 把 AR Quick Look 添加到网站上
> **编辑提示：**如果你熟悉 HTML 和 web 开发，你可以直接跳到本教程的结尾看看 demo 。 然而，如果你没有使用过 GitHub Page 构建过网站，不要错过这部分来学习构建你的第一个网站！
> 
现在，我们有了一个 iOS App 了，让我们来使用 HTML 构建一个相同功能的网站。如果你之前没有用过 HTML ，不用担心！我将会带你构建一个非常简单的网站。开始吧！

首先，在你的 Mac 上打开任意的文本编辑器。可以是 TextEdit 或任何其它相同的应用程序。我将使用 [**TextMate**](https://macromates.com/download) 。输入下面的代码：

```html
<!DOCTYPE html>
<html>
 
</html>
```
这是你开始所有 HTML 网站的方式。 `<!DOCTYPE html>` 是一个 `指令` 告诉网页浏览器关于在这个页面被写入的 HTML 的版本。我们使用的是 HTML 5 。

尖括号别成为**标签**。和我们在 Swift 中的一个 `class` 里声明所有的代码一样，所有的 HTML 代码必须声明在 `<html>` 和 `</html>` 标签中间。`<html>` 标签表示 HTML 代码的开始， `</html>` 标签是结束的标志。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p11.png)

每次你访问一个网站的时候，你都会在标签栏中看到这个网站的名字。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p12.png)  
我们看看如何在 HTML 实现这个！ 在 HTML 标签中间，输入以下代码：

```html
<head>
    <title>AR Library</title>
</head>
```
`<head>` 标签是一个所有元数据被存储的地方。一些元数据的例子包括内置的链接，脚本，图标，或是本例中的标题。

由于我们是要定义网站的标题，因此我们把标题放在 `<title>` 和 `</title>` 之间。

> **你将会注意到关于 HTML 的一点就是 strings 不在需要放一对引号包裹着了。这是最喜欢 HTML 的一点。**
>
你的文本文件应该像下面这样：

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p13.png) 

现在，我们不得不定义我们网站的 body 。这代表这你在一个网站上看到的所有文本，按钮，和图像。和之前一样，我们会在 `<body>` 标签中声明这些。在 `</head>` 下面，我们将添加以下代码：

```html
<body>
    <h1>Welcome to the AR Library</h1>
    <p>Welcome to the AR Library website. I created this website in order to view AR objects from the web on any device running iOS 12. Conincidentally, this is the first time I made a website with HTML! It's a lot of fun!</p>
</body>
```
在 `<body>` 标签里，你可以看到两个新的标签： `<h1>` 和 `<p>` 。 `H1` 代表 **Header 1** ，这通常是用于一段内容的标题。 `P` 代表这 `段落` ，这个标签被用于当你向写一些长文本的时候，你可以重命名标题和段落成你想的内容！
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p14.png) 

保存文件。确保当你保存的时候，使用 `.html` 作为后缀。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p15.png) 

点击保存的文件，它就会用 Safari (或你的默认浏览器)打开。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p16.png) 
恭喜！ 你已经用 HTML 构建了你的第一个网站！

> **你可能会思考是否可以修改字体和字体大小。这是可以用 CSS 实现。当前来说，这已经超出了本教程的范围，但你可以在[**这里**](https://www.w3schools.com/html/html_css.asp)找到很棒的文章。**
> 

### 添加 AR 按钮

现在我们的网站有一些文本，让我们来在网站里添加一些按钮去启动 AR Quick Look 视图。由于我们准备制作一个按钮，这仍然会在 `<body>` 标签里面添加代码。在 `<p>` 标签下面，输入以下内容。

```html
<a href="egg.usdz" rel="ar">
    <img src="egg.png" width=200>
</a>
```
这是定义一个超链接的 `<a>` 标签。我们上面的定义中，自定义的了几个  `<a>` 标签的属性。

1. 第一个属性是 `href` 。这是当我们的按钮被点击的时候，一个我们想要去的文件的基本路径。「文件」 是我们的 3D 模型，因此我放了一个名为 `.usdz` 的文件在那儿。
2. 第二个是 `rel` 。这指定了当前页面与链接到的页面之间的关系。我设置成 `ar` 是因为 `egg.usdz` 的关系是一个 AR 模型。
3. 现在，我们定义好按钮，但没有定义按钮的样子。通过使用 `<img src>` 标签，我定义了我们的按钮为一张图片。这样当我们的用户点击图片的时候，他们就会被导航到 AR Quick Look 视图里。我还设置了图片的 `width` 防止它太大。我使用的图片是我们已经在 Xcode 项目准备好的一张。

就这些！你可以用同样的方式添加其它按钮。
> **当你引用你的图片和 USDZ 文件时，确保这些文件和你的 HTML 文件在同一文件夹中。**

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p17.png) 

在 web 浏览器中打开文件。看一下你的第一个 HTML 网站具有一个强大的 AR 功能！

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p18.png) 

然而，当你点击一个图片时，它只会把你带到你设备中实际的文件夹。并且，没有办法在一部 iPhone 或 iPad 上查看它。这里 GitHub Pages 就来了！

### 上传到 GitHub Pages 
GitHub Pages 是一个非常棒的方式去托管静态网页。很多人使用 GitHub Pages 作为一种方式去展示个人简介或一个项目/组织的关于页面。

关于 GitHub Pages 的一个优点就是它可以在你账号下的一个仓库中编辑。通过这个，它就是一个很棒的方式去存储文件（比如图片和 AR 模型）并在你的网站中引用它们！让我们来研究一下这是如何实现的！如果你没有一点准备，在[**这儿**](https://github.com/)创建一个 GitHub 账号。
一旦你有了账号，打开主页，点击右上角的加号按钮。选中 `New repository` 。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p19.png) 

GitHub Pages 实现的方式是仅仅给定一个域名：*username*.github.io 。你创建的任何页面都是这个 URL 下面的子域名。因此，命名你的仓库为 *username*.github.io 。在下面的图片，你可以看到我命名我的仓库为 `aidev1065.github.io` ，因为 `aidev1065` 是我的用户名。你可以空着剩余的信息，点击 `Create Repository` 。
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p20.png)

当你看到仓库页面时，打开设置选项，并向下滚到 GitHub Pages 部分。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p21.png)

在 「Theme Chooser」 下面点击 `Choose a theme` 。这是为我们的页面创建一个主题。有各种各样的主题。你可以选择一个适合你的，但我打算选择 Cayman ！

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p22.png)

当你点击了 `Select theme` ，你将会看到一个 Markdown 文件。这是用于展示在我们网站的信息。如果你对 Markdown 语法不熟悉的话，不用担心。这没啥大不了的。只需要删除 `index.md` 文件的所有东西并输入以下代码：

```md
# AR Library
This is a website for an AR Library! You can view it [here](Website.html)!
```

上面最重要的就是理解括号里面的东西，把你之前创建的 HTML 文件的名字放在那里。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p23.png)

滚到最下面，点击 `Commit changes` 。现在你的 Markdown 文件已经准备好了！如果你打开任何浏览器并输入 「*username.github.io*」 ，你将会被定向到你自己的 GitHub Page !

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p24.png)

然而，当我们点击 「here」 ，我们会获得一个失效页面错误。这是因为我们还没有上传 HTML 文件， USDZ 文件， 和图片！我们开始上传！

回到仓库主页，点击 `Upload files` 按钮。

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p25.png)

现在剩下的所有事情就是上传 HTML 文件， USDZ 模型， 图片！总共有 19 个文件： *1 个 HTML 文件， 9 个模型， 和 9 张图片* 。 
![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p26.png)

滚动底部并点击 `Commit changes` 。这将花费几分钟。当所有的事情都完成了，回到你的 GitHub Page : 「*username.github.io*」 。现在，当你点击 「here」 ，你将会看到你之前创建的 HTML 网站。

同样的，当你在一个跑着 iOS 12 的设备中打开这个网站，你将会在图片的正上方看到 ARKit 的 logo 。这就意味着你可以 Quick Look 模型！ 

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p27.png)

当你点击任何图片，你得到了与 iOS 应用程序相同的视图器!

![]({{  site.url  }}/assets/screenshot/arkit-quick-look/p28.png)