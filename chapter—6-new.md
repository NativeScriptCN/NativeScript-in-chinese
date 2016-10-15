# **Chapter 6—Accessing Native APIs**

NativeScript之美，就是你可以用javascript，xml，和css写出原生ios和安卓app，而不用触及 Swift, Objective-C, 或者 Java，如果你选择的话。但是如果你想给用户奉献出一款不同的，更具平台适应性的用户界面呢？或是如果你想访问  NativeScript 没有通过 NativeScript 模块和插件暴露的iOS 或 Android API 呢？

NativeScript如愿地给予了你深入原生代码的选择，并且实现它而不用离开 JavaScript 。要看这个工作是如何运转的，我们从给IOS版本的杂货店App的 ActionBar 添加一些颜色开始。

## **目录**

* 6.1: Customize the ActionBar - iOS
* 6.2: Deleting from a list - Android
* 6.3: Deleting from a list - iOS

## [**6.1: Customize the ActionBar - iOS**](http://docs.nativescript.org/tutorial/chapter-6#61-customize-the-actionbar---ios)

When you use the ActionBar UI component, NativeScript is actually creating and managing an iOS`UINavigationController` for you. You can see this for yourself by digging into the implementation code, specifically `node_modules/tns-core-modules/ui/action-bar/action-bar.ios.js`, and`node_modules/tns-core-modules/ui/frame/frame.ios.js`.

What you might not realize is that the code that you see in these NativeScript modules, the code that looks like Objective-C-ified or Java-ified JavaScript code, is available for you to use in your own JavaScript modules. For example, you can easily get a reference to the `UINavigationBar` and simply call its [documented methods](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UINavigationBar_Class/index.html) to change its look and feel. Let's look at how to do that.

