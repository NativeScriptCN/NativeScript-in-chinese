# [**Chapter 4—模块**](http://docs.nativescript.org/tutorial/chapter-4#chapter-4nativescript-modules)

本章你会了学习 NativeScript 模块，就是你app的`node_modules/tns-core-modules` 文件夹里的JS模块。不管你是否意识到，你已经用过一些 NativeScript 模块了。包括你通过 `require()` 引入的模块（ view, frame 和 observable 模块）以及那些你在XML里面使用的UI组件（ page, image, text field 和 button 模块）。

## **目录** {#top}

* [4.1: Connecting to a backend](#4-1)
* [4.2: Dialog module](#4-2)
* [4.3: ListView](#4-3)
* [4.4: Working with arrays](#4-4)
* [4.5: GridLayout](#4-5)
* [4.6: ActivityIndicator](#4-6)
* [4.7: Animations](#4-7)

如果你深入 研究`node_modules/tns-core-modules` 你会了解这些模块是如何工作的。 我们从找到 `node_modules/tns-core-modules/camera` 文件夹开始，它包含了相机模块的实现。包括：

* 一个`package.json` 文件 用于设置模块名称；
* 一个涵盖模块的Android实现的文件 \(`camera.android.js`\)；
* 一个涵盖模块的IOS实现的文件  \(`camera.ios.js`\);
* 一个涵盖模块的androi和IOS实现的共享代码的文件 \(`camera-common.js`\)；

> ### **提示：**
> 
> ---
> 
> 你可以参考 [Node.js documentation on folders as modules](https://nodejs.org/api/modules.html#modules_folders_as_modules) 寻求更详细的信息来了解NativeScript 如何组织它的模块。

\*.ios.\* and \*.android.\*的命名约定看起来和熟悉，它正好和我们在2.4章节里用来引入 Android- 和 iOS-specific 样式的约定一样。 NativeScript 使用同样的约定在 iOS 和 Android 上实现它的模块。既然你知道这些模块在哪里了，我们就来好好看看到底它们能为你的app做些啥。

## [**4.1: Connecting to a backend**](http://docs.nativescript.org/tutorial/chapter-4#41-connecting-to-a-backend)连接后台 {#4-1}

当你在注册页面创建帐户的时候，你或许注意到数据魔术般地跑到……某个地方。事实上这里面并没有什么魔法；注册页面调用了一个 [Telerik Backend Services](http://www.telerik.com/backend-services) 提供的 RESTful 接口来为杂货铺服务注册用户。

> ### 注意：
> 
> ---
> 
> 你没必要一定要用 Telerik Backend Services来支持后台服务；在 NativeScript app里，你可以使用任何 HTTP API 。 我们在本教程里使用Telerik Backend Services 只是一个习惯，因为它让我们启动HTTP端点更快一点。

看一下 `app/shared/config.js`。这里只有一小段代码片段，但它包含一个硬编码路径指向注册页面用到的杂货店后台，你马上会使用它：

`module.exports = {`

`apiUrl: "https://api.everlive.com/v1/GWfRtXi1Lwt4jcqK/"`

`};`

> ### 提示：
> 
> ---
> 
> `config.js`同样也显示了一个方便的模式，你可以用于 在你的app里共享配置变量。

接下来，看一下`app/shared/view-models`文件夹，它包含我们预先封装好的一些视图模型，这些视图模型含有连通后台的代码。你可以通过`user-view-model.js` 文件的 `register()` 方法来印证。

> ### 注意：
> 
> ---
> 
> 在大型app里，把与后台交互的代码放在单独的文件中是很常见的，而不是直接放在视图模型里。但在本例，为了简单，交互代码直接放在了视图模型里——小型app完全有理由这么做。

注意 `register()` 方法使用 config 模块获取到后台路径，以及 [fetch](http://docs.nativescript.org/cookbook/fetch) 模块发起HTTP请求。

`var config = require("../../shared/config");`

`var fetchModule = require("fetch");`

NativeScript的 fetch 模块使用和 [browser's new fetch\(\) API](https://fetch.spec.whatwg.org/) 相同的API。因此，如果你已经知道如何使用web的 `fetch()` 方法，你就已经明白在 NativeScript 里如何发起 HTTP请求。我们通过向user视图模型添加另一个方法来看看 fetch 模块是如何工作的。

> ### 操作：在视图模型里完成登录
> 
> ---
> 
> 打开 `app/shared/view-models/user-view-model.js` 把下面的代码直接粘贴到现有的`viewModel.register()` 方法上方：
> 
> `viewModel.login = function() {`
> 
> `return fetchModule.fetch(config.apiUrl + "oauth/token", {`
> 
> `method: "POST", body: JSON.stringify({`
> 
> `username: viewModel.get("email"),`
> 
> `password: viewModel.get("password"),`
> 
> `grant_type: "password"`
> 
> `}),`
> 
> `headers: {`
> 
> `"Content-Type": "application/json"`
> 
> `}`
> 
> `})`
> 
> `.then(handleErrors)`
> 
> `.then(function(response) {`
> 
> `return response.json();`
> 
> `})`
> 
> `.then(function(data) {`
> 
> `config.token = data.Result.access_token;`
> 
> `});`
> 
> `};`

我们来分解下刚才你粘贴的代码。

* 你用 fetch 模块里的 `fetch()` 方法向存在 `shared/config.js` 里的 `apiUrl` 地址 POST 数据。 username，password 和 grant\_type 以json字符串的形式发送到这个端点 \(Telerik Backend Services [要求一个 grant\_type 参数](http://docs.telerik.com/platform/backend-services/rest/users/users-authenticate)参与登录\) 。
* `fetch()` 方法返回一个`Promise`，它允许我们在异步登录成功或失败后执行代码。你用这个功能做了三件事（那三个 `then()` 处理程序）。

  * 首先，你用在 `user-view-model.js` 底部定义的 `handleErrors()` 方法处理 HTTP response 里的任何错误。（如果你想更多了解如何在`fetch()` 工作时处理 HTTP response里的错误，请看[这篇文章](http://tjvantoll.com/2015/09/13/fetch-and-errors/) ）
  * 然后，你调用 `Response` 对象的 `json()` 方法将返回数据转换成JSON。
  * 最后，你在 config 模块里保存了一个用户授权 token 的引用。后面的教程里，你将在随后的 HTTP 请求里使用这个 token 。

  > ### **提示**
  > 
  > ---
  > 
  > * [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 是 ECMAScript 6 的一部分。因为 Promises 实现了 NativeScript 使用的两个JS引擎—android的V8 和IOS的 JavaScriptCore—Promises就可以在NativeScript apps里使用。
  > * 为方便起见， NativeScript 把 `fetch()` 做成了可用的全局变量。这意味着你可以用 `fetch()` 替换 `Module.fetch()` 。全局的 `fetch()` 快捷方式将在本教程余下的部分使用。


搞定了这些代码后，我们回到 `login.js` 来使用这个新方法。

> ### 操作：添加 **UserViewModel** 登录后台代码
> 
> ---
> 
> 在 `login.js文件的顶部`, 添加`shared/view-models/user-view-model`的引用：
> 
> `var UserViewModel = require("../../shared/view-models/user-view-model");`
> 
> **删除**下面五行代码，因为你现在要用 `UserViewModel` 来取代你前面章节添加的`user` 。
> 
> `// Remove these lines of code`
> 
> `var Observable = require("data/observable").Observable;`
> 
> `var user = new Observable({`
> 
> `email: "user@domain.com",`
> 
> `password: "password"`
> 
> `});`
> 
> 然后，在 `var UserViewModel = require(...)` 这一行后面加入下面这行代码：
> 
> `var user = new UserViewModel();`
> 
> 最后，用下面的代码替换`exports.signIn()` 方法：
> 
> `exports.signIn = function() { user.login(); };`
> 
> **注意：你可以随时在 \*\***[sample-Groceries repo](https://github.com/NativeScript/sample-Groceries/tree/end)**\*\* 的“结束”分支查看已完成的代码 。**

花点时间看下你现在的后台代码是多么干净。后台代码实例化了一个视图模型 \(`UserViewModel`\)，然后在用户点击视图里的 sign in 按钮时调用它的 `signIn()` 方法。因为视图模型是绑定了页面的两个文本域（记得`{{ email }}` 和`{{ password }}么？`），视图模型就已经有了它进行登录操作的这些数据。

如果你尝试运行app，输入你的账户凭据\(email和password\)，你实际能登录，但……你看不到任何反应。这是因为视图模型不负责更新UI。相反，视图模型返回一个 `Promise` 让后台代码处理UI。（还记得 `fetch()` 会返回一个 `Promise` 么？）。我们来看下如何使用这个 `Promise` ，并在其中介绍一个新的 NativeScript 模块。

## [**4.2: Dialog module**](http://docs.nativescript.org/tutorial/chapter-4#42-dialog-module)对话框模块 {#4-2}

要使用视图模型的 `login()` 方法返回的 `Promise` ，你需要处理两个场景：登录成功了做什么，登录不成功做什么。

在杂货店例子里，登录成功时你就将把用户导航到列表页，这个稍后我们会搭建，它允许用户在一个列表里面添加和移除杂货。完成这个导航就会用到本教程此前用过的同一个frame 模块。

棘手的情况是处理登录失败，为此你要使用 dialog 对话框模块。你可以在app里用这个模块显示几种形式的弹出界面，包括动作表，确认盒子，警告盒子和提示。这是个可高度自定义的模块，它允许你控制alert的按钮和文字，以及alert本身的消息传递。 dialog模块和其他UI小部件的代码都在 `node_modules/tns-core-modules/ui` 文件夹。我们来看如何在login页面使用这个小部件。

> ### 操作：用 **dialog** 窗体处理
> 
> ---
> 
> 添加下面的代码到 `login.js` 顶部以引入 dialog 模块：
> 
> `var dialogsModule = require("ui/dialogs");`
> 
> 接着，像这样重写 `signIn()` 方法：
> 
> `exports.signIn = function() {`
> 
> `user.login()`
> 
> `.catch(function(error) {`
> 
> `console.log(error);`
> 
> `dialogsModule.alert({`
> 
> `message: "Unfortunately we could not find your account.",`
> 
> `okButtonText: "OK"`
> 
> `});`
> 
> `return Promise.reject();`
> 
> `})`
> 
> `.then(function() {`
> 
> `frameModule.topmost().navigate("views/list/list");`
> 
> `});`
> 
> `};`

这段代码同时处理登录成功或不成功。成功时，你调用 frame 模块的`navigate()`方法 把用户导航到 list 页面（现在是空的）。失败时，你使用 dialog 模块向用户显示一个错误消息。试试输入一些无效的凭据看看 dialog是什么样子。

![](/assets/421.png)![](/assets/422.png)

有了这些，登录页面就功能完备了。既然你的NativeScript 的用户管理能运作了，我们就移步到用户管理他们杂货的列表页面去。为此，你需要一个模块来显示列表里的项目，这也就是 **ListView** 模块做的事。

> ### 提示：
> 
> 本教程从现在往后，你会经常登录，你会发现开发期间在app里硬编码你的用户凭据是很方便的。最简单的办法是向 `UserViewModel()` 构造器传递一组 email 地址和密码，例如：
> 
> `var user = new UserViewModel({ email: "username@domain.com", password: "password" });`

## [**4.3: ListView**](http://docs.nativescript.org/tutorial/chapter-4#43-listview)数据列表模块 {#4-3}

ListView部件允许你在屏幕上显示一些东西的列表，就像你想要显示杂货的列表。在杂货列表和后台API连接之前，我们来看看如何在屏幕上显示一个硬编码的列表。

> ### 操作：构造列表视图
> 
> ---
> 
> `打开app/views/list/list.xml 并粘贴下面的代码， 它创建了你的杂货陈列的列表：`
> 
> `<Page loaded="loaded">`
> 
> `<GridLayout>`
> 
> `<ListView items="{{ groceryList }}">`
> 
> `<ListView.itemTemplate>`
> 
> `<Label text="{{ name }}" horizontalAlignment="left" verticalAlignment="center"/>`
> 
> `</ListView.itemTemplate>`
> 
> `</ListView>`
> 
> `</GridLayout>`
> 
> `</Page>`
> 
> ### 提示：
> 
> ---
> 
> 注意页面将使用 `<GridLayout>` 在屏幕上布局UI组件。当你添加更多的UI组件，你会开始把屏幕切分成行和列，但现在你将只用 `<ListView>` 占据整个屏幕（这是 `<GridLayout>` 没有属性时的默认行为）。

之前说过，即便你在XML里使用 `<ListView>` ， ListView 模块也仍然是一个 NativeScript 模块。你可以在 `node_modules/tns-core-modules/ui/list-view` 文件夹里找到它的实现。如果你想，你可以[像这个例子那样](http://docs.nativescript.org/cookbook/ui/list-view)，在后台代码文件中用纯JavaScript 构建一个ListView。通常情况下在XML里使用 NativeScript UI组件更方便些，所以我们在本教程里通篇采用XML的用法。

注意 `<ListView.itemTemplate>` 的用法。这个标签赋予你控制在列表里如何显示 ListView的每个子项目的能力。现在你只用了一个简单的 `<Label>` UI组件来显示每个杂货的 `{{ name }}` 。

如果你原样运行这些代码，你不会看到杂货列表有任何东西。首先你需要建立一种方法来管理ListView模块里面的数据，为此你需要一个新的 NativeScript 模块：也就是 ObservableArray 。

## **[4.4: Working with arrays](http://docs.nativescript.org/tutorial/chapter-4#44-working-with-arrays)****\*\***\*\*_\*\_\*_\*\_\*_\*\_\*_\*\_\*_\*\_\*_\*\_\*_\*使\_\*用数组 {#4-4}

在本教程之前的部分你见过如何创建 observables 并如何用它们通过后台代码文件和视图模型来联接XML视图。在本节你将要做同样的事情，一个额外的联接，它涉及到使这个 `items` 属性生效。

`<ListView items="{{ groceryList }}">`

ListView组件的 `items` 属性采用了一个数组，为了在视图模型上创建这个数组， NativeScript 提供了一个特别的 ObservableArray 模块。要看它是如何工作的，我们开始来构建 list 页面的后台代码。

> #### **操作: Populate the list view**填充列表视图
> 
> ---
> 
> 打开 `app/views/list/list.js` 并粘贴下面的代码：
> 
> `var dialogsModule = require("ui/dialogs");`
> 
> `var Observable = require("data/observable").Observable;`
> 
> `var ObservableArray = require("data/observable-array").ObservableArray;`
> 
> `var page;`
> 
> `var pageData = new Observable({`
> 
> `groceryList: new ObservableArray([`
> 
> `{ name: "eggs" },`
> 
> `{ name: "bread" },`
> 
> `{ name: "cereal" }`
> 
> `])`
> 
> `});`
> 
> `exports.loaded = function(args) {`
> 
> `page = args.object;`
> 
> `page.bindingContext = pageData;`
> 
> `};`

这里，你将创建一个新的 Observable 对象名为 `pageData` ，你在 `load()` 方法里将它设置为页面的 `bindingContext` 。 在Observable 内部，你单独设置了一个 `"groceryList"` 属性作为 ObservableArray 类的一个新实例。注意 `"groceryList"` 属性如何与 `<ListView items="{{ groceryList }}">` 对应，也注意数组里面的每个 `"name"` 属性如何与 `<Label text="{{ name }}">对应` 。如果运行app你会看到list界面显示出硬编码数据：

![](/assets/441.png)![](/assets/442.png)

既然我们在屏幕上有了项目，我们来看下这个列表如何与后台联接以替换掉硬编码数据。为此你要用一个视图模型来改变list页面，就像我们在login页面做的那样。

本页面初始的视图模型已经在位于 `app/shared/view-models/grocery-list-view-model.js` 的文件里了，它包含的代码看起来很像你已有的 `list.js` 文件里的。

`var config = require("../../shared/config");`

`var fetchModule = require("fetch");`

`var ObservableArray = require("data/observable-array").ObservableArray;`

`function GroceryListViewModel(items) {`

`var viewModel = new ObservableArray(items);`

`return viewModel;`

`}`

`module.exports = GroceryListViewModel;`

我们来扩展它以把这个视图模型和后端联接。

> ### **操作: 从后端填充列表视图**
> 
> ---
> 
> 你要从使用 `GroceryListViewModel` 改变 `list.js` 开始。首先， `require()` 引入 `GroceryListViewModel` 才能使用它：
> 
> `var GroceryListViewModel = require("../../shared/view-models/grocery-list-view-model");`
> 
> 接着，**移除**现有的 `var pageData` 定义：
> 
> `// Remove these seven lines of code`
> 
> `var pageData = new Observable({`
> 
> `groceryList: new ObservableArray([`
> 
> `{ name: "eggs" },`
> 
> `{ name: "bread" },`
> 
> `{ name: "cereal" }`
> 
> `])`
> 
> `});`
> 
> 在相同的位置增加下面的代码：
> 
> `var groceryList = new GroceryListViewModel([]);`
> 
> `var pageData = new Observable({`
> 
> `groceryList: groceryList`
> 
> `});`
> 
> 最后，用下面的方法代码替换现有的 `exports.loaded()` 方法，它在视图模型层调用了两个新的方法—— `empty()` 和 `load()` 。
> 
> `exports.loaded = function(args) {`
> 
> `page = args.object;`
> 
> `page.bindingContext = pageData;`
> 
> `groceryList.empty();`
> 
> `groceryList.load();`
> 
> `};`
> 
> 这段代码里， `groceryList` 指向的是  grocery  list模型，并且 `empty()` 方法清空了list，然后调用视图模型的 `load()` 方法从后端重新加载数据。
> 
> 让这一切起作用的最后一段代码实际上是在视图模型里实现 `empty()` 和`load()` 方法。打开 `app/shared/view-models/grocery-list-view-model.js` ，在 `var viewModel` 定义和 `return viewModel` 语句之间粘贴下面的代码：
> 
> `viewModel.load = function() {`
> 
> `return fetch(config.apiUrl + "Groceries", {`
> 
> `headers: {`
> 
> `"Authorization": "Bearer " + config.token`
> 
> `}`
> 
> `})`
> 
> `.then(handleErrors)`
> 
> `.then(function(response) {`
> 
> `return response.json();`
> 
> `}).then(function(data) {`
> 
> `data.Result.forEach(function(grocery) {`
> 
> `viewModel.push({`
> 
> `name: grocery.Name,`
> 
> `id: grocery.Id`
> 
> `});`
> 
> `});`
> 
> `});`
> 
> `};`
> 
> `viewModel.empty = function() {`
> 
> `while (viewModel.length) {`
> 
> `viewModel.pop();`
> 
> `}`
> 
> `};`

上述代码发起了一个有点眼熟的HTTP对话，因为它利用了你在前面部分用过的同一个 fetch 模块，该 fetch 模块 的第一个 `then()` 处理器 检查  HTTP 的错误，第二个 `then()` 处理器 把数据从 response 转换为 JSON 格式，第三个处理器把每个杂物项目从 response 推送到 ObservableArray 里面。

如果你运行app并使用email地址“ user@nativescript.org ”和密码“ password ”登录，你会看到像下面这样的杂物列表：

![](/assets/443.png)![](/assets/444.png)

这里最酷的是你不用写代码。注意这里没有必要刷新UI，或者手动获取 ListView UI组件——所有视图模型做的只是把JSON response 推送给 ObservableArray ，UI就自己处理了。

我们来看看你如何能在此基础上构建并允许用户直接从app添加到他们的杂物列表。

## [**4.5: GridLayout**](http://docs.nativescript.org/tutorial/chapter-4#45-gridlayout)格子布局 {#4-5}

你已经知道如何添加项目到一个 `<ListView>` 了，因为你刚刚在上一节这样做了——需要做的就是调用 ObservableArray 模块的 `push()` 方法。

为了让用户能管理他们的杂货列表，你将不得不在屏幕上放置更多的UI组件，为此你将需要用`<GridLayout>` UI组件把屏幕分割成行和列。

> ### **操作: 实现添加杂货到列表**
> 
> ---
> 
> 打开 `app/views/list/list.xml` 用下面的代码更改`<GridLayout>` 标签：
> 
> `<GridLayout rows="auto, *" columns="2*, *">`
> 
> 这里的`rows`属性把屏幕切分成两行，第一行参照它的子组件高度自动设置，另一行包含\*，或者说屏幕剩余的高度。 `columns` 属性把屏幕分成两列，第一列取得屏幕2\/3宽度，第二列取得剩下三分之一。
> 
> 然后，为了给给用户添加杂货到列表的路径，在页面添加一个文本域和一个按钮。把下面两行代码添加到紧接着初始  `<GridLayout>`标签下面：
> 
> `<TextField id="grocery" text="{{ grocery }}" hint="Enter a grocery item" row="0" col="0" />`
> 
> `<Button text="Add" tap="add" row="0" col="1" />`
> 
> 该`TextField`** **有一个id属性 `"grocery"`，并绑定到页面的 binding  context 的 `{{ grocery }}` 属性。按钮的 `tap` 行为指向一个 `add()` 方法，你稍后会在后台代码文件添加它。
> 
> 但这里最需要注意的是 `row` 和`col` 属性的用法。这些属性是0-基数的，所以 `TextField` 的 `row="0" col="0"` 属性把它放在了第一行第一列，而 `Button` 的 `row="0" col="1"` 属性把它放在了第一行第二列。
> 
> 最后，在第二行里用下面的代码替换 `<ListView>` 标签，让它跨占两列，也给它一个id属性（你后面会用到的）。
> 
> `<ListView items="{{ groceryList }}" id="groceryList" row="1" colSpan="2">`
> 
> ### _以上是xml代码更改，以下是后台代码更改_
> 
> ---
> 
> 现在你只需对后台代码文件做一些必要的更改来支持XML的更改。打开`list.js，`首先添加一个新的 `"grocery"` 属性到 `pageData`Observable 。 `pageData` 定义看起来应是这样的：
> 
> `var pageData = new Observable({`
> 
> `groceryList: groceryList,`
> 
> `grocery: ""`
> 
> `});`
> 
> 然后，你需要添加一个 `add()` 方法处理按钮点击事件。粘贴下面的代码到 `list.js` 底部：
> 
> `exports.add = function() {`
> 
> `// Check for empty submissions`
> 
> `if (pageData.get("grocery").trim() === "") {`
> 
> `dialogsModule.alert({`
> 
> `message: "Enter a grocery item",`
> 
> `okButtonText: "OK"`
> 
> `});`
> 
> `return;`
> 
> `}`
> 
> `// Dismiss the keyboard`
> 
> `page.getViewById("grocery").dismissSoftInput();`
> 
> `groceryList.add(pageData.get("grocery")) .catch(function() {`
> 
> `dialogsModule.alert({`
> 
> `message: "An error occurred while adding an item to your list.",`
> 
> `okButtonText: "OK"`
> 
> `});`
> 
> `});`
> 
> `// Empty the input field`
> 
> `pageData.set("grocery", "");`
> 
> `};`
> 
> 在这个方法里，你首先确定用户没有未输入杂货就提交。如果用户有输入，收起输入键盘，就从页面的绑定上下文（这里绑定到那个新的 `<TextField>` ）得到 `"grocery"` 属性，然后把这个值传给视图模型的 `add()` 方法。
> 
> 最后，定义这个 `add()` 方法。为此，打开 `app/shared/view-models/grocery-list-view-model.js` 并粘贴下面的方法到 `empty()` 方法的下面，但要在 `return viewModel` 语句之前：
> 
> `viewModel.add = function(grocery) {`
> 
> `return fetch(config.apiUrl + "Groceries", {`
> 
> `method: "POST",`
> 
> `body: JSON.stringify({`
> 
> `Name: grocery`
> 
> `}),`
> 
> `headers: {`
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
> `.then(function(response) { return response.json(); })`
> 
> `.then(function(data) {`
> 
> `viewModel.push({ name: grocery, id: data.Result.Id });`
> 
> `});`
> 
> `};`

搞定后回到你的app，你会发现你能添加杂货项目了且能立即出现在列表里——并且，所有这些完全由后台服务驱动。够屌吧？

![](/assets/451.gif)![](/assets/452.gif)

我们来看下如何通过一个显示活动的指示器来优化这个页面。

## **[4.6: ActivityIndicator](http://docs.nativescript.org/tutorial/chapter-4#46-activityindicator)**活动指示器 {#4-6}

目前当你首次访问列表页时，杂货出现前有一些延迟。这个延迟可能会困扰新用户，他可能觉得app卡住了，而不是正在从后端检索数据。

在 NativeScript app里，当你的app忙于运行的时候，你可以在你的UI里使用 ActivityIndicator 模块来显示一个旋转图标。该活动指示器是个相当简单的UI组件，因为它主要使用一个属性—— `busy` 。当一个活动指示器的 `busy` 属性被设置为`true`时活动指示器显示，当 `busy` 属性被设置为false的时候不显示。我们通过添加一个活动指示器到列表页面来看下这个模块是如何工作的。

> #### **操作：添加一个活动指示器**
> 
> ---
> 
> 打开 `app/views/list/list.xml` 吧下面的元素直接添加到 结束标签`</GridLayout>`前面。
> 
> `<ActivityIndicator busy="{{ isLoading }}" rowSpan="2" colSpan="2" />`
> 
> 然后, 在 `app/views/list/list.js`里面， 用下面的四行代码 替换现有的 `groceryList.load()` 也就是`loaded()`：
> 
> `pageData.set("isLoading", true);`
> 
> `groceryList.load().then(function() {`
> 
> `pageData.set("isLoading", false);`
> 
> `});`

上面的代码里，你添加了一个新的 `"isLoading"` 标识到列表页的 Observable ，然后将活动指示器的 `busy`属性与它的值绑定。 在页面的 `loaded()` 方法里，你设定 `"isLoading"` 标识的初始值为 `true` ，这将显示活动指示器。当杂货列表完成加载，你反转 `"isLoading"` 标识回到 `false` ，活动指示器隐藏。

你通过设置活动指示器的 `rowSpan` 和 `colSpan` 属性控制它的显示位置。本例的`rowSpan="2" colSpan="2"` 让活动指示器占据了其父组件 GridLayout 的两行和两列。下面试这个新的活动指示器的表现：

![](/assets/461.gif)![](/assets/462.gif)

列表页现在看起来亲和多了，但我们可以用一个更加强大的模块来提升用户体验：**动画模块**。

## **[4.7: Animations](http://docs.nativescript.org/tutorial/chapter-4#47-animations)**动画 {#4-7}

运行的稳健和高性能的动画的能力是人们选择制作原生移动app的最主要原因之一， 而NativeScript 让运行动画变简单了。 NativeScript 动画模块提供一个 [JavaScript APIs套件](http://docs.nativescript.org/ui/animation) 让你用运行很多种动画来处理屏幕上的元素，包括如下：

* [Opacity不透明度](http://docs.nativescript.org/ui/animation#opacity)
* [Background Color背景色](http://docs.nativescript.org/ui/animation#background-color)
* [Translations移动](http://docs.nativescript.org/ui/animation#translate)
* [Scaling放大缩小](http://docs.nativescript.org/ui/animation#scale)
* [Rotating旋转](http://docs.nativescript.org/ui/animation#rotate)

在我们的列表页面你将使用一个[不透明度动画](http://docs.nativescript.org/ui/animation#opacity)在数据加载后来淡入显示你的杂货列表。我们来添加代码然后探讨下它是如何工作的。

> #### **操作: 添加一个动画**
> 
> ---
> 
> 打开 `app/views/list/list.css` 添加一个 `opacity: 0` 规则到现有的 `ListView` 选择器。 全套规则应该是这样：
> 
> `ListView {`
> 
> `margin: 5;`
> 
> `opacity: 0;`
> 
> `}`
> 
> 然后，在 `app/views/list/list.js`里，用下面的代码替换现有的 `exports.loaded()` 方法，它新做了两件事情：获取一个 `<ListView>`\(`page.getViewById("groceryList")`\) 的引用，然后在 `groceryList.load()` 调用完成后调用 `<ListView>` 这个元素的 `animate()` 方法。
> 
> `exports.loaded = function(args) {`
> 
> `page = args.object;`
> 
> `var listView = page.getViewById("groceryList");`
> 
> `page.bindingContext = pageData;`
> 
> `groceryList.empty();`
> 
> `pageData.set("isLoading", true);`
> 
> `groceryList.load().then(function() {`
> 
> `pageData.set("isLoading", false);`
> 
> `listView.animate({`
> 
> `opacity: 1,`
> 
> `duration: 1000`
> 
> `});`
> 
> `});`
> 
> `};`

一些新的状况在上面的代码里发生了。

首先，在CSS里，你把杂货列表 `<ListView>` 的 `opacity` 定义为0。这使得杂货列表在页面加载的时候完全隐藏。然后，JavaScript 里， `groceryList.load()` 调用完成之后，你调用了列表视图的 `animate()` 方法。它把该元素的 `opacity` 在一秒内从0（完全隐藏）变成了1（完全可见）。

> **注意**:  **animation 方法的 **`duration`** 属性采用毫秒计数.。因此, **`1000`** 就是1秒。**

The result of this code is a nice fade-in animation:

这段代码的结果就是一个不错的淡入动画：

![](/assets/471.gif)![](/assets/472.gif)

动画模块玩起来很有意思，并且使用简单。你需要做的就是用 `getViewById()` 获取元素的引用，然后调用这个元素的 `animate` 方法。或许你想花几分钟通篇了解下我们的 [animation samples](http://docs.nativescript.org/ui/animation#examples) 并在你的杂货店里尝试里面的一些动画。

既然你完成了登录、注册和列表页面，我们来提升app的功能把它作为一个杂货列表的管理工具。下一章你将添加email验证、社交分享等功能。你会使用 NativeScript的最有用的内动来完成它：npm模块。

> **提示：**
> 
> ---
> 
> 你安装 NativeScript 后盒子里冒出的模块太多了，以至于我们不能在本教程里一一涵盖——包括 [location service定位服务](http://docs.nativescript.org/cookbook/location)， [file-system helper文件系统助手](http://docs.nativescript.org/cookbook/file-system)， [timer module时间模块](http://docs.nativescript.org/cookbook/timer) ，[camera module相机模块](http://docs.nativescript.org/cookbook/camera)，[color module颜色模块](http://docs.nativescript.org/cookbook/color)，更多更多。 一定要细读 官方文档的“Modules API” ，或者全面看看 `node_modules/tns-core-modules` 以了解所有哪些可以使用。

