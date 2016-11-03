# **[Modules](http://docs.nativescript.org/core-concepts/modules#modules)**

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

* [ui\/frame](http://docs.nativescript.org/cookbook/ui/frame): Provides the `Frame` class which represents the logical `View` unit that is responsible for navigation within an application.
* [ui\/page](http://docs.nativescript.org/cookbook/ui/page): Provides the `Page` class which represents a logical unit for navigation inside a `Frame`. NativeScript apps consist of pages.
* [color](http://docs.nativescript.org/cookbook/color): Lets you create colors which you can use when you style the UI.
* [text\/formatted-string](http://docs.nativescript.org/cookbook/formatted-string): Provides the `FormattedString` and `Span` classes which you can use to create rich text formatted strings.
* [xml](http://docs.nativescript.org/cookbook/xml-parser): Provides the `XmlParser` class which is a SAX parser using the easysax implementation.
* [ui\/styling](http://docs.nativescript.org/cookbook/ui/styling): Provides the `Style` class which is responsible for the visual appearance of elements.
* [ui\/animation](http://docs.nativescript.org/cookbook/ui/animation): Provides the `Animation` class which lets you animate view properties.

### 布局

* [ui\/layouts\/stack-layout](http://docs.nativescript.org/cookbook/ui/layouts/stack-layout): Provides the `StackLayout` class which lets you arrange the children of the layout in a single line.
* [ui\/layouts\/grid-layout](http://docs.nativescript.org/cookbook/ui/layouts/grid-layout): Provides the `GridLayout` class which lets you arrange the children of the layout in a flexible grid area with columns and rows.
* [ui\/layouts\/absolute-layout](http://docs.nativescript.org/cookbook/ui/layouts/absolute-layout): Provides the `AbsoluteLayout` class which lets you arrange the children of the layout at arbitrary positions or draw them in multiple layers.
* [ui\/layouts\/wrap-layout](http://docs.nativescript.org/cookbook/ui/layouts/wrap-layout): Provides the `WrapLayout` class which lets you arrange the children of the layout at sequential positions from left to right and then wrap the lines of children from top to bottom.

### 窗口小部件

* [ui\/activity-indicator](http://docs.nativescript.org/cookbook/ui/activity-indicator): Provides the `ActivityIndicator` class which represents a widget for showing that a service is currently busy.
* [ui\/button](http://docs.nativescript.org/cookbook/ui/button): Provides the `Button` class which is a standard button widget.
* [ui\/label](http://docs.nativescript.org/cookbook/ui/label): Provides the `Label` class which is a standard label widget.
* [ui\/text-field](http://docs.nativescript.org/cookbook/ui/text-field): Provides the `TextField` class which represents an editable single-line box.
* [ui\/text-view](http://docs.nativescript.org/cookbook/ui/text-view): Provides the `TextView` class which represents an editable multi-line line box.
* [ui\/list-view](http://docs.nativescript.org/cookbook/ui/list-view): Provides the `ListView` class which represents a standard list view widget.
* [ui\/image](http://docs.nativescript.org/cookbook/ui/image): Provides the `Image` class which represents an image widget.
* [ui\/progress](http://docs.nativescript.org/cookbook/ui/progress): Provides the `Progress` class which represents a progress or loading indicator.
* [ui\/scroll-view](http://docs.nativescript.org/cookbook/ui/scroll-view): Provides the `ScrollView` class which represents a scrollable area that can show content which is larger than the visible area.
* [ui\/search-bar](http://docs.nativescript.org/cookbook/ui/search-bar): Provides the `SearchBar` class which represents a standard search bar component.
* [ui\/slider](http://docs.nativescript.org/cookbook/ui/slider): Provides the `Slider` class which represents a standard slider component.
* [ui\/switch](http://docs.nativescript.org/cookbook/ui/switch): Provides the `Switch` class which represents a standard switch component.
* [ui\/tab-view](http://docs.nativescript.org/cookbook/ui/tab-view): Provides the `TabView` class which represents a standard content component with tabs.
* [ui\/web-view](http://docs.nativescript.org/cookbook/ui/web-view): Provides the `WebView` class which represents a standard browser widget.
* [ui\/html-view](http://docs.nativescript.org/cookbook/ui/html-view): Provides the `HtmlView` class which represents a standard html view widget.
* [ui\/dialogs](http://docs.nativescript.org/cookbook/ui/dialogs): Lets you show various dialogs such as alerts, prompts, confirmations and others.
* [ui\/list-picker](http://docs.nativescript.org/cookbook/ui/list-picker): Provides the `ListPicker` class which represents a standard list picker component.
* [ui\/date-picker](http://docs.nativescript.org/cookbook/ui/date-picker): Provides the `DatePicker` class which represents a standard date picker component.
* [ui\/time-picker](http://docs.nativescript.org/cookbook/ui/time-picker): Provides the `TimePicker` class which represents a standard time picker component.
* [ui\/placeholder](http://docs.nativescript.org/ui/placeholder): Provides the `Placeholder` class which lets you add a native widget to the visual tree.

## [**WHATWG Polyfills**](http://docs.nativescript.org/core-concepts/modules#whatwg-polyfills)

* [fetch](http://docs.nativescript.org/cookbook/fetch): The `Fetch` polyfill that provides requests, responses, and the process that binds them: fetching. [https:\/\/fetch.spec.whatwg.org\/](https://fetch.spec.whatwg.org/)

