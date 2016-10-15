# **Chapter 6—Accessing Native APIs**

NativeScript之美，就是你可以用javascript，xml，和css写出原生ios和安卓app，而不用触及 Swift, Objective-C, 或者 Java，如果你选择的话。但是如果你想给用户奉献出一款不同的，更具平台适应性的用户界面呢？或是如果你想访问  NativeScript 没有通过 NativeScript 模块和插件暴露的iOS 或 Android API 呢？

NativeScript如愿地给予了你深入原生代码的选择，并且实现它而不用离开 JavaScript 。要看这个工作是如何运转的，我们从给IOS版本的杂货店App的 ActionBar 添加一些颜色开始。

## **目录**

* 6.1: 自定义 ActionBar - iOS
* 6.2: 从列表中删除 - Android
* 6.3: 从列表中删除 - iOS

## **[6.1: 自定义ActionBar - iOS](http://docs.nativescript.org/tutorial/chapter-6#61-customize-the-actionbar---ios)**

当你使用 ActionBar UI 组件的时候，, NativeScript 实际上在为你创建和管理一个  iOS`UINavigationController`  。你可以通过深入实现的代码自己了解这点，特别是`node_modules/tns-core-modules/ui/action-bar/action-bar.ios.js`和`node_modules/tns-core-modules/ui/frame/frame.ios.js`文件。

你可能意识不到的是，你在这些 NativeScript 模块里看到的代码，这些看起来有些 Objective-C化或者 Java化的JavaScript代码，是可以让你用在你自己的 JavaScript 模块里的。例如，你可以轻易地得到 `UINavigationBar` 的一个引用然后简单地调用它的 [documented 方法](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html) 来改变其外观和感觉。我们来看如何做到。

> ### **操作: 修改 ActionBar**
> 
> ---
> 
> 在你开始捣鼓 `UINavigationBar` 之前，我们添加一个 `<ActionBar>` 到登录和注册页面，那么app的三个页面都有统一的外观了。
> 
> 打开 `app/views/register/register.xml` 并 紧接着开标签 `<Page>` 粘贴下面的代码：
> 
> `<Page.actionBar>`
> 
> `<ActionBar title="Sign up">`
> 
> `</ActionBar>`
> 
> `</Page.actionBar>`
> 
> 然后，打开 `app/views/login/login.xml` 并粘贴下面的代码, 同样紧接着开标签 `<Page>` ；
> 
> `<Page.actionBar>`
> 
> `<ActionBar title="Sign in"></ActionBar>`
> 
> `</Page.actionBar>`
> 
> 最后， 打开 `app/app.css` 粘贴下面的CSS 以修改 ActionBar的颜色；
> 
> `ActionBar {`
> 
> `color: white;`
> 
> `background-color: #2E6DAD;`
> 
> `}`
> 
> ActionBar就位了，我们来看如何用一些原生IOS API来自定义它的外观。打开 `p/views/login/login.js` 并在 `exports.loaded()` 函数里面粘贴下面的代码，紧接着 `ar page = args.object;` 语句：
> 
> `if (page.ios) {`
> 
> `var navigationBar = frameModule.topmost().ios.controller.navigationBar;`
> 
> `navigationBar.barStyle = UIBarStyle.UIBarStyleBlack;`
> 
> `}`

好了，我们看下刚才发生的事情，从 `if (page.ios)` 检查开始。通常，NativeScript 模块遵循的模式是通过IOS和android属性来暴露它们的原生实现。你可以在 if 检查 \(`page.ios`\) 里看到这点，并且在if检查的第一行， `meModule.topMost().ios` 被用来检索一个底层 `UINavigationController` 的引用。对现有的这些属性 \(比如`if (page.ios)`\) 的测试是一个方便的方法来分支你的代码，以确信IOS特定代码只圆形在IOS商，而 Android 的特定代码只运行在 Android 上。



