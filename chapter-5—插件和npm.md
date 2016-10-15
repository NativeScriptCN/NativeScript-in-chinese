当你创建更复杂的app时，你可能会遇到不是在 NativeScript 模块里实现的功能。不要担心，因为 NativeScript 允许你使用npm（node包管理）来导入npm模块到你的app。或者，如果需要的话，你可以安装 NativeScript 插件，它们是简单的npm模块，可以用来访问本地代码并使用安卓和ios SDK。

在本章，你将安装和使用一个外部的email验证器模块，当邮件地址在注册页面输入的时候验证地址格式。然后，你会添加一个 NativeScript 插件， [NativeScript 社交分享](https://www.npmjs.com/package/nativescript-social-share) ，以允许用户用他们设备本地的共享程序来分享他们的杂货列表。

## **[Table of contents](http://docs.nativescript.org/tutorial/chapter-5#table-of-contents)**目录

* 5.1: 使用npm模块
* [5.2: 使用NativeScript 插件](#5-2)

## **[5.1: Using npm modules](http://docs.nativescript.org/tutorial/chapter-5#51-using-npm-modules)**使用npm模块 {#5-1}

能够确保人们在注册页面输入准确格式的email地址到你的app里是非常好的。你可以自己写这个功能，但验证邮件地址是令人惊讶地棘手（ [surprisingly tricky](http://stackoverflow.com/questions/46155/validate-email-address-in-javascript) ），所以使用一个已经提供验证的npm模块是更简单的。对于杂货铺，我们来看看如何添加这个 [邮件验证模块](https://www.npmjs.com/package/email-validator) 来测试地址是否有效。

> ### 操作：安装email验证模块
> 
> ---
> 
> 回到你的命令行，确定你在杂货铺项目的文件夹根目录工作，也就是这样：
> 
> `sample-Groceries <----------------`
> 
> `├── app`
> 
> `│ └── ...`
> 
> `├── package.json`
> 
> `└── platforms`
> 
> `├── android`
> 
> `└── ios`
> 
> 在此根目录安装email验证模块：
> 
> `npm install email-validator --save`

安装进程在后台搞了一些飞机。首先，因为你添加了 `--save` 标志，npm记录这个依赖到你app的 `package.json` 。如果打开 `package.json` ，你会看到 `"email-validator"` 在你app的 `"dependencies"` 数组.。

`"dependencies": {`

`"email-validator": "^1.0.4"`

`}`

npm CLI同样在你app根目录里创建了一个 `node_modules` 文件夹。此文件夹包含email验证模块的代码，就是在 `node_modules/email_validator/index.js` 里的一些验证逻辑。

> ### 提示：
> 
> 通过在 `package.json` 文件保存你app的npm依赖，你总是可以运行 `npm install` 来再生你的 `node_modules` 文件夹。也是因为这点，通常的操作是在源码管理里排除 `node_modules` 文件夹。杂货铺app使用git作源码管理，也因此在它的 `.gitignore` 里包括了 `ode_modules/`

既然你安装了模块，我们就来看如何使用它。

#### 

> ### 操作：使用email验证模块
> 
> ---
> 
> 打开 `/app/shared/view-models/user-view-model.js`  将下面这行代码添加到文件顶部：
> 
> `var validator = require("email-validator");`
> 
> ### **注意：**
> 
> ---
> 
> NativeScript 框架的 `require()` 方法被设置为查看npm模块的 `package.json` 文件的 `"main"` 值。在当前模块里， `"main"` 值是 `"index.js"` 。因此，当你运行 `require("email-validator")` ，你实际上在请求位于 `node_modules/email_validator/index.js` 的文件。你也可以输入 `require("email-validator/index")` 检索到相同的文件。
> 
> 要利用验证器，添加一个函数到 `user-view-model.js` ，正好在 `return viewModel` 行的上面：
> 
> `viewModel.isValidEmail = function() {`
> 
> `var email = this.get("email");`
> 
> `return validator.validate(email);`
> 
> `};`
> 
> Then, edit the registration function in `app/views/register/register.js` to trap any malformed email addresses:
> 
> 然后，在 `app/views/register/register.js` 编辑注册函数来分离任何畸形的email地址：
> 
> `exports.register = function() {`
> 
> `if (user.isValidEmail()) {`
> 
> `completeRegistration();`
> 
> `} else {`
> 
> `dialogsModule.alert({`
> 
> `message: "Enter a valid email address.",`
> 
> `okButtonText: "OK"`
> 
> `});`
> 
> `}`
> 
> `};`

在这个函数里，用户提交一个email和密码，值被发送到视图模型去验证。如果通过，注册可以畸形，否则你显示一个警告。然而为了彻底检验这个改变（指的是添加验证器这回事），你需要做另外一件事情。

> ### 操作：重建app
> 
> 我们在第一章提过，虽然 `tns livesync` 命令足够聪明地重载app来应付你对app的大多数更改，某些更改需要全部重建——主要是，在 `app/App_Resources` 里对本地文件的改变，通过 `npm install` 安装了新的模块，和新的 NativeScript 插件。
> 
> For NativeScript to recognize this new email-validator npm module, type `Ctrl+C` in your terminal to kill the existing `tns livesync` watcher if it’s still running, and then use `tns run` to rebuild your application and deploy it to an emulator or device.
> 
> 要让 NativeScript 认识这个新的email验证器 npm 模块，在你的命令行按 `Ctrl+C` 终止现有的 `tns livesync` 监视，如果它在继续运行的话，然后使用 `tns run` 来重建你的应用并部署到虚拟机或者设备上。
> 
> `tns run ios --emulator`
> 
> 或
> 
> `tns run android --emulator`
> 
> app部署之后你可以再次运行 `livesync` 命令来重启监视。
> 
> `tns livesync ios --emulator --watch`
> 
> 或
> 
> `tns livesync android --emulator --watch`

app再次运行之后，如果你视图哦那个一个无效的email地址注册，你会看到阻止提交的警告：

![](/assets/511.png)![](/assets/512.png)

通常，npm模块大大扩展你在 NativeScript app里能做的事情的数量。需要如期时间格式化？使用 [moment](https://www.npmjs.com/package/moment) 。需要多用途函数处理对象和数组？使用 [lodash](https://www.npmjs.com/package/lodash) 或 [underscore](https://www.npmjs.com/package/underscore) 。当你把 NativeScript 插件引入到这个场景时，这个代码重用的效力会变得更加强大。

> ### 警告：
> 
> **不是所有npm模块在 NativeScript app里都能工作，依赖于 Node.js 或 browser APIs 的没用，因为 NativeScript 里没有这些 APIs 。 NativeScript wiki 涵盖了一个\*\***[被验证过能在NativeScript apps里运行的一些相当流行的npm模块的清单](https://github.com/NativeScript/NativeScript/wiki/supported-npm-modules)**\*\*  。**

## [**5.2: Using NativeScript plugins**](http://docs.nativescript.org/tutorial/chapter-5#52-using-nativescript-plugins)**\*\***\*\*_\*使用\_\* NativeScript 插件 {#5-2}

NativeScript插件是添加了运行原生代码和使用ios\/android框架的功能的npm模块。因为 NativeScript 插件也是npm模块，你在此前部分学习的那么多技巧仍然有效。最大的不同之处是在命令行里用来安装插件的方法。我们通过安装  NativeScript 社交分享插件来看看它是如何工作的。

> ### 操作： ** 安装社交分享插件**
> 
> ---
> 
> 回到命令行，确定你仍然在app的根目录，运行下面的命令：
> 
> `tns plugin add nativescript-social-share`

安装进程做了与 `npm install` 命令相同的事情——包括从npm检索模块，安装模块到 `node_modules` 里面，并且把模块作为一个依赖保存到 `ckage.json` ——但是 `tns plugin add` 命令而外地设置了插件需要使用的所有原生代码。

例如 [NativeScript push插件](https://github.com/NativeScript/push-plugin) 使用了ios和android SDK，那么 `tns plugin add` 命令就妥善安装了它们。 [NativeScript flashlight插件](https://github.com/tjvantoll/nativescript-flashlight) 需要授权使在安卓上用摄像头，那么 `tns plugin add` 命令就同样地负责设置好。

既然你安装了社交分享插件，我们就来看如何使用它。

> ### 操作：使用社交分享插件
> 
> ---
> 
> 打开`app/views/list/list.js` 在文件顶部添加下面这行，它会引入你刚才安装的社交分享模块：
> 
> `var socialShare = require("nativescript-social-share");`
> 
> 然后你需要创建一些UI元素来允许你分享杂货列表。为此，打开 `app/views/list/list.xml` ，紧跟开标签 `<Page>` 后且在开标前 `<GridLayout>` 前添加下面的代码：
> 
> `<Page.actionBar>`
> 
> `<ActionBar title="Groceries">`
> 
> `<ActionBar.actionItems>`
> 
> `<ActionItem text="Share" tap="share" ios.position="right" />`
> 
> `</ActionBar.actionItems>`
> 
> `</ActionBar>`
> 
> `</Page.actionBar>`
> 
> 这段代码定义了一个 [ActionBar](http://docs.nativescript.org/ui/action-bar) ，它是一个UI组件可以包含不同的目录项目，并包裹在 `<ActionBar.actionItems>标签里。`  ActionBar 的 `title` 允许你显示页面具体的标题。
> 
> ### 注意：
> 
> ios设备上， `<ActionItem>` 是有序地从左到右地放置；你可以通过提供一个 `ios.position` 属性来覆盖它们（就像上面的代码那样）
> 
> 模块安装并引入之后，且UI就位，你可以在列表页面的后台代码文件里实现 `<ActionItem>` 的tap处理器（ `share()` ）了。
> 
> 为此，回到 `list.js` 文件，粘贴下面的代码到文件底部：
> 
> `exports.share = function() {`
> 
> `var list = [];`
> 
> `var finalList = "";`
> 
> `for (var i = 0, size = groceryList.length; i < size ; i++) {`
> 
> `list.push(groceryList.getItem(i).name);`
> 
> `}`
> 
> `var listString = list.join(", ").trim();`
> 
> `socialShare.shareText(listString);`
> 
> `};`

这段代码从杂货列表的视图模型抓取杂货，把数据转换为逗号分隔的字符串，并把字符串传递给社交分享模块的 `shareText()` 方法。

> ### 警告：
> 
> ---
> 
> 在这节安装了一个 NativeScript 插件，你必须最后一次重建app来测试改变。如果你不记得如何做，[回到本章开始](#5-1)去查看。

现在如果你运行app，你会看到在屏幕顶端有一个新的按钮。当你点击它，会出现原生的 iOS 或 Android 分享挂件，让你发送你的杂货到社交网络，或者通过email，短信，或者任何你喜欢的方式发送它们。

酷毙了吧，哈？使用npm模块的能力极大地扩展了你在一个 NativeScript app里能做的事情。需要在你的app里构建邮件？试试 [NativeScript email plugin](https://www.npmjs.com/package/nativescript-email) 。需要在你的app里使用剪贴板？试试 [NativeScript clipboard plugin](https://www.npmjs.com/package/nativescript-clipboard) 。

如果你在寻找 NativeScript 插件，可以从搜索 [Telerik NativeScript 插件市场](http://plugins.telerik.com/nativescript)和我们的[community-curated list of plugins on npm](http://plugins.nativescript.rocks/) 开始。如果没找到你要的插件，你可以 [在我们的点子入口请求插件](https://nativescript.ideas.aha.io/) ，或者你可以尝试 [自己创造这个插件](https://docs.nativescript.org/plugins) 。

Between NativeScript modules, npm modules, and NativeScript plugins, the NativeScript framework provides a lot of functionality you can use to build your next app. However, we've yet to talk about NativeScript's most powerful feature: the ability to directly access iOS and Android APIs in JavaScript. Let's look at how it works.

在 NativeScript 模块，npm模块，和 NativeScript 插件之间， NativeScript 框架提供了大量功能让你可以用来创建你的下一个app。然而，我们尚未谈到 NativeScript最强大的内容：在 JavaScript 里面直接访问 iOS 和 Android 的API。我们来看看它是如何工作的 。

