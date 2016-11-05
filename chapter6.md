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

好了，我们看下刚才发生的事情，从 `if (page.ios)` 检查开始。通常，NativeScript 模块遵循的模式是通过IOS和android属性来暴露它们的原生实现。你可以在 if 检查 \(`page.ios`\) 里看到这点，并且在if检查的第一行， `meModule.topMost().ios` 被用来检索一个底层 `UINavigationController` 的引用。对现有的这些属性 \(比如`if (page.ios)`\) 的测试是一个方便的方法来分支你的代码，以确信IOS特定代码只运行在IOS上，而 Android 的特定代码只运行在 Android 上。

> ### 提示：
> 
> ---
> 
> 最好的操作是，当你有一些不多的特定平台的改变要实施的时候，用一个 **if **检查来测试平台是可行的路径。相反地，如果你有大量的，完全不同的代码块针对IOS和android的话，你可能希望采取  [特定平台的后台代码文件](http://docs.nativescript.org/tutorial/chapter-6#platform-specific-files) ——比如： `login.ios.js` 和 `login.android.js` 。

在`if`块里面，你从获取一个 `UINavigationBar` 的引用开始，然后设置它的 `barStyle`[ property](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIKitDataTypesReference/index.html#//apple_ref/c/tdef/UIBarStyle) 到 `UIBarStyle.UIBarStyleBlack` ，这\(反直觉地？\)让IOS状态栏使用白色文本。它产生的外观显示如下：

![](/assets/6.1.png)

学习如何将IOS和ANDROID API 转换为 NativeScript 代码会有个尝试和试错的过程，直到掌握。你可以随时参考 NativeScript 文档里详细描述是如何处理代码转换的。这里是 [Android的文档](http://docs.nativescript.org/runtimes/android/marshalling/java-to-js.html)，这里是[iOS的文档](http://docs.nativescript.org/runtimes/ios/marshalling/Marshalling-Overview.html)。

> ### 提示：
> 
> ---
> 
> NativeScript为所有 IOS和ANDROID API提供了 TypeScript 声明文件。如果你使用 TypeScript 就可以引用这些声明文件以在你的编辑器里以使编码实现。即便你没有使用 TypeScript ，当你要转换原生API为 NativeScript 代码时，这些声明文件也可以是宝贵的参考资料。例如要视图在IOS声明文件里搜索 “UINavigationBar” 看那些其它的属性可用。
> 
> * [iOS TypeScript 声明文件](https://github.com/NativeScript/NativeScript/tree/master/tns-platform-declarations/ios/objc-i386)
> * [Android TypeScript 声明文件](https://raw.githubusercontent.com/NativeScript/NativeScript/master/tns-platform-declarations/android/android17.d.ts)

分叉用户体验带来的不只是简单地改变一些颜色。比如，华东删除列表项目是一个常用的IOS互动行为，而Andriod木有。但正如你看到的， NativeScript 使其比较容易地分叉你的代码以提供一个更加适配平台的体验。那么要允许用户从列表中删除一个项目，我们就先为IOS创建一个滑动删除UI，然后使用一个更**安卓范**的垃圾筐图标来让用户从Android App删除项目。

## [**6.2: 从列表删除 - Android**](http://docs.nativescript.org/tutorial/chapter-6#62-deleting-from-a-list---android)

对于安卓，你准备添加一个可点击的垃圾筐到杂货列表的每一项；那么首要的挑战就是指出如何只是在安卓里面显示这些图片，而在IOS上使用一个完全不同的UI。

为此你将在XML使用文件里使用一些新的语法。 NativeScript 允许你使用 `platform:attributeName` 语法来为单独的平台设置属性。例如下面的代码将按钮的文本在IOS上设置为“foo”，而在Android上设置为“bar”。

> `<Button ios:text="foo" android:text="bar" />`

同样的语法对于所有UI组件的所有属性都有效，并且这个机制是 NativeScript 允许你叉分你的代码来区分iOS和Android实现的一个可行的路径。

> ### **操作: 添加一个针对安卓的UI元素**
> 
> ---
> 
> 打开 `app/views/list/list.xml，` 找到 `<ListView.itemTemplate>` 标签， 用下面的代码替换：
> 
> `<ListView.itemTemplate>`
> 
> `<GridLayout columns="*, auto">`
> 
> `<Label text="{{ name }}"/>`
> 
> `<Image src="res://ic_menu_delete" ios:visibility="collapsed" col="1" tap="delete" />`
> 
> `</GridLayout>`
> 
> `</ListView.itemTemplate>`

通过这段代码你主要添加了一个 `<Image>` 到现有的 ListView 模板。但既然你在这个模板里有了复合UI组件，你就需要告诉 NativeScript 如何布局这两个组件，这也就是 `<GridLayout>` 处理的问题。通过定义 `columns="*, auto"` 你把每个项目分成两栏：第一栏包含 label ，第二栏包含新的图片。

For the image itself, the `ios:visibility="collapsed"` attribute sets the image's `visibility` CSS property to `"collapsed"`, which hides it. Because the attribute was prefixed with `ios:`, that CSS property is only applied on iOS; therefore the button displays on Android devices, but not on iOS ones. The trash can image itself has already been placed in the app for you, and can be found in appropriate sizes in the four drawable folders in `/app/App_Resources/Android`. Here's what the trash can UI looks like on Android:

对于图片本身， `ios:visibility="collapsed"` 属性设置图片的 `visibility` CSS属性为 `collapsed"` ，把它隐藏起来。因为该属性以 `ios:` 开头，这个CSS属性就只在IOS上生效；因此按钮会显示在Android上。垃圾筐图片本身已经在app里为你放置好了，以合适的尺寸放置在在四个文件夹里，能在 `app/App_Resources/Android` 里找到。这里是垃圾筐UI在Android上看起来的样子：

![](/assets/621.png)

最后，让垃圾筐实际地删除项目。为此你需要在列表的后台代码文件实现 `tap="delete"` 处理器。

> ### **操作: 创建删除函数**
> 
> ---
> 
> 打开 `app/views/list/list.js` 粘贴下面的代码到文件底部：
> 
> `exports.delete = function(args) {`
> 
> `var item = args.view.bindingContext;`
> 
> `var index = groceryList.indexOf(item);`
> 
> `groceryList.delete(index);`
> 
> `};`
> 
> 这段代码获取了用户点击的杂货的index，将其与视图模型里相应的项目匹配，然后传递这个index带视图模型的 `delete()` 方法——这还不存在，所以我们来创建它。
> 
> 打开 `app/shared/view-models/grocery-list-view-model.js` 并粘贴下面的代码。记住直接添加这个函数到文件末尾，正好在 `return viewModel` 行上面：
> 
> `viewModel.delete = function(index) {`
> 
> `return fetch(config.apiUrl + "Groceries/" + viewModel.getItem(index).id, {`
> 
> `method: "DELETE", headers: {`
> 
> `"Authorization": "Bearer " + config.token,`
> 
> `"Content-Type": "application/json"`
> 
> `}`
> 
> `})`
> 
> `.then(handleErrors)`
> 
> `.then(function() {`
> 
> `viewModel.splice(index, 1);`
> 
> `});`
> 
> `};`

这段代码现在看起来可能相当熟悉。你再次调用了 fetch 模块的 `fetch()` 方法，这次是定义一个 `"DELETE"` 方法来从后台删除一个杂货。你再次返回一个 `Promise` 所以回调函数能处理调用成功和失败的调用。再次注意使用MVVM方式来搭建App的能力。为了更新杂货列表UI，所有你要做的就是从 ObservableArray \(`viewModel.splice(index, 1)`\) 里删除该项目，并让列表的呈现自己处理变动。

If you run your app on Android you should be able to delete items from the list.

如果你在Android上运行app，你就可以从列表删除项目了。

![](/assets/622.gif)

既然你为安卓的点击图标创建了接口，我们就来为IOS添加滑动删除接口。

## [**6.3: 从列表删除 - iOS**](http://docs.nativescript.org/tutorial/chapter-6#63-deleting-from-a-list---ios)

如果你是IOS用户你可能相当熟悉互动删除手势，因为它普遍存在于很多IOS应用。实现该手势的代码实际上已经合并到 iOS SDK 本身了\(查看`UITableViewCellEditingStyle`[ 文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/#//apple_ref/c/tdef/UITableViewCellEditingStyle) \)，所以你可以直接在你的 NativeScript app里使用这些API。

实现这个的代码比我们先前的Adroid示例要稍微麻烦些，所以我们在 `shared/utils/ios-swipe-delete.js` 文件准备了构建好的模块来实现这个功能。在这个文件里你能找到一个自定义的支持 `UITableViewDataSource` 协议的数据源的实现。该文件输出了一个单独的  `eable()` 函数，它拿到一个 ListView 的引用，并自定义数据源行为注入到 ListView 。

不要对这些代码的具体行为太过担心，因为它涉及到一些IOS API怎样工作的理解。最酷的是你可以用几十行代码就可以实现这个比较高级的IOS API实现，并且用一个非常简单的 JavaScript API 就可以很容易地封装这些代码。这个使用上的简单正是为什么 NativeScript 模块和 NativeScript 插件那么容易运用。我们来看看如何使用这个模块。

> ### **操作: 编辑 ListView**
> 
> ---
> 
> 添加下面的代码到 `app/views/list/list.js`顶部：
> 
> `var swipeDelete = require("../../shared/utils/ios-swipe-delete");`
> 
> 然后，添加下面的代码到 `exports.loaded()` 函数，直接在 `page = args.object;` 语句下面：
> 
> `if (page.ios) {`
> 
> `var listView = page.getViewById("groceryList");`
> 
> `swipeDelete.enable(listView, function(index) {`
> 
> `groceryList.delete(index);`
> 
> `});`
> 
> `}`

这节代码拿到一个页面的 `<ListView>` id的引用ing传递这个引用到滑动删除模块的 `enable()` 函数。这个 `enable()` 函数 同样拿到一个回调，那么你另外通过一个内联函数调用你在前节创建的视图模型的 `delete()` 函数。这里是滑动删除功能在IOS上看起来的样子：

![](/assets/631.gif)

然后……就搞定了！您已经创建了一个功能性，跨平台，后端驱动的app来管理您的杂货列表。在这个进程中你为Android和IOS都创建了一个唯一的UI，利用了 leveraged 插件和npm模块，学会了如何登录和注册，管理后台服务，通过添加和删除功能创建了列表，和更多……

恭喜你！尽情的 [在Twitter](https://twitter.com/intent/tweet?text=I%20just%20built%20an%20iOS%20and%20Android%20app%20using%20@NativeScript%20%F0%9F%8E%89.%20You%20can%20too!%20http://docs.nativescript.org/tutorial/chapter-0%20%23opensource) 或 [Facebook](https://www.facebook.com/sharer/sharer.php?u=http%3A%2F%2Fdocs.nativescript.org%2Ftutorial%2Fchapter-0&p%5B) 上 [share分享你的成就](https://twitter.com/intent/tweet?text=I%20just%20built%20an%20iOS%20and%20Android%20app%20using%20@NativeScript%20%F0%9F%8E%89.%20You%20can%20too!%20http://docs.nativescript.org/tutorial/chapter-0%20%23opensource) ，让你的朋友们咋舌吧！

> ### **提示：**
> 
> ---
> 
> * 如果你对 NativeScript 如何实现直接调用IOS和Android API好奇，你可以在我们的博客阅读 [“How NativeScript Works”](http://developer.telerik.com/featured/nativescript-works/) 。
> * 记住 [Groceries app's “end” branch](https://github.com/NativeScript/sample-Groceries/tree/end) 有这个教程的最终状态。 你可以随时回到这里参考。
> * 高级的 ListView 交互，比如滑动删除, 拖动刷新, 以及其它的组件比如日历和图表都在盒子之外作为Telerik的 [UI For NativeScript](http://docs.telerik.com/devtools/nativescript-ui/introduction)产品实现了。（《=========也就是可能有收费！！！）

