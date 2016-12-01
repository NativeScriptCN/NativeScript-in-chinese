在本章你将开始基础的东西，包括安装NativeScript CLI，建立一个新的项目，和建立、运行你的第一个app。

## **内容列表**

* [1.1: 安装 NativeScript 并配置环境](#1-1)

* [1.2: 创建你的app](#12-开始你的app)

* [1.3: 增加目标开发平台](#13-添加目标开发平台)

* [1.4: 运行你的app](#14-运行你的-app)

* [1.5: 开发工作流](#15-开发流程)


## **1.1: ** 安装 NativeScript 并配置环境 {#1-1}

NativeScript CLI有一些系统需求，你必须在制作app之前处理完备。在我们开始制作前，请确定你完成了 NativeScript 的安装指南

* [完成安装指导](http://docs.nativescript.org/start/quick-setup)

## [**1.2: 开始你的app**](http://docs.nativescript.org/tutorial/chapter-1#12-start-your-app)

NativeScript CLI安装后，就可以开始创建你的app了。通常，你要**使用 **`tns create`** 命令来创建一个空的 NativeScript 应用**。本教程里，我们搭建了一个样板工程作为“杂货店”的起点。

### **练习: 获取“杂货店”起点**

切换到你想要保存app代码的文件夹：

> `cd the-folder-you-want-groceries-to-be-in`

然后, 假定你安装了git, 从github 克隆Groceries repo:

> `git clone https://github.com/NativeScript/sample-Groceries.git`

接下来, 进入新克隆的 repo's 文件夹:

> `cd sample-Groceries`

最后, 切换到 “start” 分支 便是本教程的起点了:

> `git checkout start`

**提示: “end” 分支有本教程的最终被状态。 如果你被卡住了可以随时跳到这个分支。**

## **[1.3: 添加目标开发平台](http://docs.nativescript.org/tutorial/chapter-1#13-add-target-development-platforms)**

你的app已经搭建好了，但在你运行它之前，您需要为您打算开发的每个目标平台初始化一个特定于平台的本地项目

### **操作: 添加ios和安卓平台**

如果你在MAC上, 添加iOS 平台:

> `tns platform add ios`

然后, 用同样的命令添加安卓平台:

> `tns platform add ios`

**注意: 你只有在开发设备上安装好SDK后才能添加平台. 如果你运行**`tns platform add`**命令出现错误, 请回看开发设备环境配置的部分。 **

## [**1.4: 运行你的 app**](http://docs.nativescript.org/tutorial/chapter-1#14-running-your-app)

平台初始化完成，你就可以在真机或虚拟机上运行app了。

### **操作: 运行app**

ios+虚拟机上运行

> `tns run ios --emulator`

![](/assets/1.4.1.png)

安卓+虚拟机上运行

> `tns run android --emulator`

**注意：**

* 你的开发设备上至少要有一个安卓虚拟设备以使本命令生效。如果出现错误，尝试安装一个AVD\(安卓虚拟机\)后再次运行此命令。

* 如果你在使用 [Genymotion](https://www.genymotion.com/), 运行你的 Genymotion 虚拟机, 然后运行`tns run android`.


如果一切正常，你可以看到app在虚拟设备上运行了

![](/assets/1.4.2.png)

运行 NativeScript apps 的一些提示：

**提示**

* 要运行USB连接的安卓或ios设备，使用同样的run命令但不要 `--emulator` 标签就可以了，比如：

  `tns run android` 和 `tns run ios`

* `tns device` 命令会列出所有USB连接的ios设备，USB连接的安卓设备，和Genymotion 虚拟设备 ，`tns run能正常运行`. 注意`tns device` 不会列出iOS 设备.


## **[1.5: 开发流程](http://docs.nativescript.org/tutorial/chapter-1#15-development-workflow)**

此时，你已经下载安装了 NativeScript CLI ，运行app的 iOS 和 Android 条件也齐备。现在你需要一个好的工作流程以使你的更改即时呈现，为此，我们使用 `tns livesync` 命令。

### 操作**: 你的第一次 NativeScript 更改**

如果你此前的 `tns run ios` 或者 `tns run android` 任务还在运行， 在命令行里使用 `Ctrl+C` 结束它。

如果你使用Mac, 执行以下命令以启动一个 iOS livesync 监视 :

> `tns livesync ios --emulator --watch`

如果你的app运行在安卓虚拟机上, 执行以下命令以启动一个 android livesync 监视:

> `tns livesync android --emulator --watch`

如果你的app运行在USB连接的 Android 设备或 Genymotion 许你设备上, 运行同样的命令时去掉--emulator 标签:

> `tns livesync android --watch`

tns livesync 命令传送更新后的代码到你的设备或虚拟机以更新app。 通过添加--watch 标签,livesync命令额外地监视你NativeScript项目里的文件. 一旦某个文件改变, 该命令检测到变动, 把变动的代码更新到你的app。

**提示:** 你可以阅读 [how NativeScript works](http://developer.telerik.com/featured/nativescript-works/)了解这是如何做到的。

为了看见 livesync生效，我们来对你的app做点小更改。

用你的编辑器打开app的`app/views/login/login.xml` 文件，更改 `<Label text="hello world" />` 为 `<Label text="hello NativeScript" />，`**保存**`app/views/login/login.xml` ，就会看到app重新运行并现实更改后的文本了。

---

不管你是运行在ios还是安卓上，或者你是使用 `tns livesync` 还是`tns run` ， 当app运行时，NativeScript CLI 都会显示 `console.log()` 的输出结果，以及出错时的堆栈跟踪报告。所以如果你的app在本教程期间的任何时候崩溃，查看命令行的详细出错报告。

ios和android日志可能有点呱噪，你或许不得不滚动鼠标寻找实际的问题在那。比如说，如果我要调用 `foo.bar()` 但 `foo并不存在，下面就是我在ios上得到的信息：`

`/app/path/to/file.js:14:8: JS ERROR ReferenceError: Can't find variable: foo 1 0xe3dc0 NativeScript::FFICallback<NativeScript::ObjCMethodCallback>::ffiClosureCallback(ffi_cif*, void*, void**, void*)`

下面是安卓日志里同样的信息：

> `E/TNS.Native( 2063): ReferenceError: foo is not defined`
> 
> `E/TNS.Native( 2063): File: "/data/data/org.nativescript.groceries/files/app/./views/login/login.js, line: 13, column: 4`

### **提示：**

> 当你要调试一个问题时，你总可以尝试在你的 JavaScript 代码里加入 `console.log()语句——就像你在那些基于浏览器的应用一样。`

### 注意：

> 在 NativeScript app里，不是所有改动都会动态反应。例如，LiveSync不能修补本地配置文件的变化 \(`Info.plist`,`AndroidManifest.xml，` 等等\)，新插件的安装，以及任何需要应用完全编译的任何其他变动。在这些情况下，你可能需要使用 `Ctrl+C` 停止 livesync ，回到应用文件夹使用 `tns run ios或` `tns run android命令。` 别担心，在教程中要完全编译的情况出现时，这些说明将被明确列出。

既然你已经创建了app，配置了环境，并在ios和android上安装了app准备运行，你就准备好了深入研究构建app的文件了

