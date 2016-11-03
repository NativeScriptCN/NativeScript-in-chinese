# **[模块](http://docs.nativescript.org/core-concepts/modules#modules)**

为了让你使用目标平台的本地设备和平台功能， NativeScript 采用一个模块化的设计模式。所有设备，平台或UI功能都位于独立的模块内部。要使用模块提供的功能，你就需要 **require** 该模块。

在你的项目里，每个模块的文件驻留在 `tns_modules` 目录下一个专用的子目录。每个默认模块伴都有一个 `package.json` 文件，用来声明在你的调用之内该模块如何被调用，并且说明了哪个文件包含模块自身的代码。

> `{`
> 
> `"name" : "button",`
> 
> `"main" : "button.js"`
> 
> `}`

* [核心模块](http://docs.nativescript.org/core-concepts/modules#core-modules)
* [设备功能模块](http://docs.nativescript.org/core-concepts/modules#device-functionality-modules)
* [D数据模块](http://docs.nativescript.org/core-concepts/modules#data-modules)
* [用户界面模块](http://docs.nativescript.org/core-concepts/modules#user-interface-modules)

  * [Layouts](http://docs.nativescript.org/core-concepts/modules#layouts)
  * [Widgets](http://docs.nativescript.org/core-concepts/modules#widgets)

* [WHATWG polyfills](http://docs.nativescript.org/core-concepts/modules#whatwg-polyfills)

  ## [**核心模块**](http://docs.nativescript.org/core-concepts/modules#core-modules)

* [application](http://docs.nativescript.org/cookbook/application): 为应用抽象提供相关的方法（就是把App当作一个对象？！）。

* [console](http://docs.nativescript.org/cookbook/console): 让你打印消息到设备控制台。

* [application-settings](http://docs.nativescript.org/cookbook/application-settings): 让你存取任何你的应用相关的信息。

* [http](http://docs.nativescript.org/cookbook/http): 让你发送web请求并接收响应。

* [image-source](http://docs.nativescript.org/cookbook/image-source): 提供了 `ImageSource` 类，就是具体平台图片对象的抽象类，被当作图片源使用（通常是bmp位图）。

* [timer](http://docs.nativescript.org/cookbook/timer): 让你创建，启动，停止和重启定时器。

* [trace](http://docs.nativescript.org/cookbook/trace): 让你基于分类跟踪和打印特定信息。

* [ui\/image-cache](http://docs.nativescript.org/cookbook/ui/image-cache): 提供 `image-cache`  类，它处理图片下载请求并缓存已经下载的图片。
* [connectivity](http://docs.nativescript.org/cookbook/connectivity): 让你检查互联网连接并监控其变化。

## [**设备功能模块**](http://docs.nativescript.org/core-concepts/modules#device-functionality-modules)

* [camera](http://docs.nativescript.org/cookbook/camera): 让你用设备的相机照像。
* [location](http://docs.nativescript.org/cookbook/location): 让你使用设备的定位传感器。
* [platform](http://docs.nativescript.org/cookbook/platform): 提供有关设备，操作系统和软件的信息。
* [fps-meter](http://docs.nativescript.org/cookbook/fps-meter): 让你捕获你app的每秒帧数标准（帧数表）。
* [file-system](http://docs.nativescript.org/cookbook/file-system): 让你用设备的文件系统工作。 为文件系统实体提供高层次的抽象，如文件、文件夹、已知文件夹、路径、分隔符等。
* [ui\/gestures](http://docs.nativescript.org/cookbook/ui/gestures): 提供 `GesturesObserver` 类，它让你发现和响应用户手势。

## [**数据模块**](http://docs.nativescript.org/core-concepts/modules#data-modules)

* [data\/observable](http://docs.nativescript.org/cookbook/data/observable): 提供 `Observable` 类， 在 MVVM 模式里它代表一个可观察的数据或对象。
* [data\/observable-array](http://docs.nativescript.org/cookbook/data/observable-array): 提供 `ObservableArray` 类，它在一个对象集合里面检测并响应变动。
* [data\/virtual-array](http://docs.nativescript.org/cookbook/data/virtual-array): 提供 `VirtualArray` 类， 它是一个高级的类似数组的类，有助于按需加载项目（懒加载？）。

## [**UI模块**](http://docs.nativescript.org/core-concepts/modules#user-interface-modules)

* [ui\/frame](http://docs.nativescript.org/cookbook/ui/frame): 提供 `Frame` 类代表逻辑 `View` 单元，它在一个app里负责导航。
* [ui\/page](http://docs.nativescript.org/cookbook/ui/page): 提供 `Page` 类 ， 代表一个逻辑单元用以在 `Frame` 里导航 。NativeScript App 包括若干page。
* [color](http://docs.nativescript.org/cookbook/color): 让你创建颜色，以便你变换UI样式时使用。
* [text\/formatted-string](http://docs.nativescript.org/cookbook/formatted-string): 提供 `FormattedString` 和 `Span` 类， 你可以用它们创建富文本格式化字符串。
* [xml](http://docs.nativescript.org/cookbook/xml-parser): 提供`XmlParser` 类， 这是一个用easysax实现的SAX解析器。
* [ui\/styling](http://docs.nativescript.org/cookbook/ui/styling): 提供`Style` 类， 它负责元素的视觉表现。
* [ui\/animation](http://docs.nativescript.org/cookbook/ui/animation): 提供 `Animation` 类， 它可以让你动画视图属性。

### 布局

* [ui\/layouts\/stack-layout](http://docs.nativescript.org/cookbook/ui/layouts/stack-layout): 提供 `StackLayout` 类， 它让你在一行内安排子元素的布局。
* [ui\/layouts\/grid-layout](http://docs.nativescript.org/cookbook/ui/layouts/grid-layout): 提供 `GridLayout` 类， 它让你在一个弹性的格子里用行和列安排子元素的布局。
* [ui\/layouts\/absolute-layout](http://docs.nativescript.org/cookbook/ui/layouts/absolute-layout): 提供 `AbsoluteLayout` 类，它让你在任意位置安排子元素的布局，或在多个层呈现他们。
* [ui\/layouts\/wrap-layout](http://docs.nativescript.org/cookbook/ui/layouts/wrap-layout): 提供 `WrapLayout` 类 ，它让你按次序地从左到右，然后从上到下（卷滚）安排子元素布局。

### 窗口小部件

* [ui\/activity-indicator](http://docs.nativescript.org/cookbook/ui/activity-indicator): 提供 `ActivityIndicator` 类， 它表示一个窗口小部件，用于显示当前正在忙的服务。
* [ui\/button](http://docs.nativescript.org/cookbook/ui/button): 提供 `Button` 类， 它是标准的按钮部件。
* [ui\/label](http://docs.nativescript.org/cookbook/ui/label): 提供`Label`类 ，它是标准的label 部件。
* [ui\/text-field](http://docs.nativescript.org/cookbook/ui/text-field): 提供 `TextField` 类， 它表示一个可编辑的单行方框。
* [ui\/text-view](http://docs.nativescript.org/cookbook/ui/text-view): 提供 `TextView` 类， 它表示一个可编辑的多行方框 。
* [ui\/list-view](http://docs.nativescript.org/cookbook/ui/list-view): 提供 `ListView` 类， 它表示一个标准的 list view 部件。
* [ui\/image](http://docs.nativescript.org/cookbook/ui/image): 提供`Image` 类， 代表一个图片部件。
* [ui\/progress](http://docs.nativescript.org/cookbook/ui/progress): 提供`Progress` 类， 代表一个进度或加载指示器。
* [ui\/scroll-view](http://docs.nativescript.org/cookbook/ui/scroll-view): 提供`ScrollView` 类， 代表一个可滚动区域，以显示超出屏幕可视区域的内容。
* [ui\/search-bar](http://docs.nativescript.org/cookbook/ui/search-bar): 提供`SearchBar` 类，代表一个标准的搜索条部件。
* [ui\/slider](http://docs.nativescript.org/cookbook/ui/slider): 提供 `Slider` 类， 代表一个标准的滑块部件。
* [ui\/switch](http://docs.nativescript.org/cookbook/ui/switch):提供 `Switch` 类， 代表一个标准的 switch 部件。
* [ui\/tab-view](http://docs.nativescript.org/cookbook/ui/tab-view): 提供`TabView` 类，  代表一个标准的 标签内容部件。
* [ui\/web-view](http://docs.nativescript.org/cookbook/ui/web-view): 提供 `WebView` 类， 表示一个标准的浏览器部件。
* [ui\/html-view](http://docs.nativescript.org/cookbook/ui/html-view): 提供 `HtmlView` 类， 代表一个标准的 html 视图部件。
* [ui\/dialogs](http://docs.nativescript.org/cookbook/ui/dialogs): 让你显示各种对话框，比如警告框，提示框，确认框和其他。
  [ui\/list-picker](http://docs.nativescript.org/cookbook/ui/list-picker): 提供 `ListPicker` 类， 表示一个标准的列表选择组件。
* [ui\/date-picker](http://docs.nativescript.org/cookbook/ui/date-picker): 提供 `DatePicker` 类， 表示一个标准的日期选择组件。
* [ui\/time-picker](http://docs.nativescript.org/cookbook/ui/time-picker): 提供 `TimePicker` 类， 表示一个标准的时间选择组件。
* [ui\/placeholder](http://docs.nativescript.org/ui/placeholder): 提供 `Placeholder` 类， 让你可以添加一个原生窗口部件到视觉树。

## **[WHATWG Polyfills](http://docs.nativescript.org/core-concepts/modules#whatwg-polyfills)**

* [fetch](http://docs.nativescript.org/cookbook/fetch): The `Fetch` polyfill that provides requests, responses, and the process that binds them: fetching. [https:\/\/fetch.spec.whatwg.org\/](https://fetch.spec.whatwg.org/)

