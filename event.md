# **事件**

* [Overview概览](http://docs.nativescript.org/core-concepts/events#overview)
* [Adding an event handler添加一个事件处理器](http://docs.nativescript.org/core-concepts/events#adding-an-event-handler)
* [Removing an event listener移除事件监听器](http://docs.nativescript.org/core-concepts/events#removing-an-event-listener)
* [PropertyChange event属性变动 事件](http://docs.nativescript.org/core-concepts/events#propertychange-event)
* [Creating a custom event创建自定义事件](http://docs.nativescript.org/core-concepts/events#creating-a-custom-event)
* [Avoiding memory leaks避免内存泄漏](http://docs.nativescript.org/core-concepts/events#avoiding-memory-leaks)
* [Working with weak events用弱事件工作](http://docs.nativescript.org/core-concepts/events#working-with-weak-events)

## [**概览**](http://docs.nativescript.org/core-concepts/events#overview)

事件是从事件发射器发送的消息，以表示特定行为的发生。此行为可以由用户行为（如点击）或程序逻辑（例如，指示从服务器下载图像已完成）生成。引发事件的对象叫作**事件发送器**（简称**发送器**）或者**事件触发器**。消费事件的对象叫作**事件监听器**（简称**监听器**）或者**事件处理器**。

NativeScript框架提供了一个 `Observable` 类支持用事件工作的进程。有关这方面更多信息查看 [API Reference](http://docs.nativescript.org/api-reference/classes/_data_observable_.observable.html) 。因为这是NativeScript框架的基本类之一，几乎所有 NativeScript 对象（组件）都有处理事件的选项。

## [**添加事件处理器**](http://docs.nativescript.org/core-concepts/events#adding-an-event-handler)

添加事件处理器意思是设置一个函数（方法），以在事件引发的时候执行。

**示例 1** 显示了如何设置一个函数，当按钮点击的时候在控制台打印输出“Hello World”。你可以在简化语法和完整语法中选择，或是可以在XML中声明一个事件处理器。

下面的例子显示了如何用简化语法和完整语法添加一个事件处理器。这里有个可选的第三个参数代表 `this` 参数。

### [**示例1 \(JS\): 用简化和完整语法添加一个事件处理器或事件监听器**](http://docs.nativescript.org/core-concepts/events#example-1-javascript-adding-an-event-handler-or-an-event-listener-using-the-short-and-full-syntax)

> `//Adding a listener with the short syntax简化语法`
> 
> `var buttonModule = require("ui/button");`
> 
> `var testButton = new buttonModule.Button();`
> 
> `testButton.text = "Test";`
> 
> `testButton.on(`
> 
> `buttonModule.Button.tapEvent,`
> 
> `function (eventData) { console.log("Hello World!"); },`
> 
> `this`
> 
> `);`
> 
> `//Adding a lister with the full syntax完整语法`
> 
> `var testButton2 = new buttonModule.Button();`
> 
> `testButton2.text = "Test";`
> 
> `var onTap = function (eventData) { console.log("Hello World!"); };`
> 
> `testButton2.addEventListener(buttonModule.Button.tapEvent, onTap, this);`

### [**示例 1 \(XML\): 用XML声明添加事件处理器或事件监听器**](http://docs.nativescript.org/core-concepts/events#example-1-xml-adding-an-event-handler-or-an-event-listener-using-an-xml-declaration)

> `<Page>`
> 
> `<StackLayout>`
> 
> `<Button tap="onTap" />`
> 
> `</StackLayout>`
> 
> `</Page>`

你需要一个后台代码文件（看示例2）来写函数体（后台代码文件有相同的文件名，但不同的后缀：.js或.ts，取决于你使用什么语言）。

### [**示例 2: 关联按钮点击事件**](http://docs.nativescript.org/core-concepts/events#example-2-hooking-to-a-button-tap-event)

> **JS**
> 
> ---
> 
> `function onTap(eventData) {`
> 
> `console.log("Hello World!");`
> 
> `}`
> 
> `exports.onTap = onTap;`
> 
> **TS**
> 
> ---
> 
> `export function onTap(eventData) {`
> 
> `console.log("Hello World!");`
> 
> `}`

## [**移除事件监听器**](http://docs.nativescript.org/core-concepts/events#removing-an-event-listener)

通常你没必要移除事件监听器。当你需要只接收一次事件或者释放资源时，你可以这样做。在此情况下，你可以使用**示例3**的方法。

> ### **没有语法来通过XML声明移除事件监听器。**

### **[示例 3: 移除按钮点击事件监听器](http://docs.nativescript.org/core-concepts/events#example-3-removing-a-button-tap-event-listener)**

> **JS**
> 
> ---
> 
> `//Removing a listener with short syntax简单语法`
> 
> `testButton.off(buttonModule.Button.tapEvent);`
> 
> `//Removing a listener with full syntax完整语法`
> 
> `testButton2.removeEventListener(buttonModule.Button.tapEvent);`

## [**属性变动事件**](http://docs.nativescript.org/core-concepts/events#propertychange-event)

`Observable`类提供一个内置事件叫作 `propertyChange` ，当属性改变时该事件被调用。**示例4** 显示了如何订阅该事件。

### **示例 4: 处理 propertyChange 事件**

> ### JS
> 
> ---
> 
> `var observableModule = require("data/observable");`
> 
> `var observableObject = new observableModule.Observable();`
> 
> `observableObject.on(`
> 
> `observableModule.Observable.propertyChangeEvent,`
> 
> `function(propertyChangeData){`
> 
> `console.log(propertyChangeData.propertyName + " has been changed and the new value is: " + propertyChangeData.value);`
> 
> `}`
> 
> `);`

这里主要的是要注意 `propertyChange` 事件对整个数据绑定系统是至关重要的。要充分利用数据绑定机制，你必须要做的是让你的业务对象继承 `Observable` 类。**示例5**演示了如何做到这点。

### **示例5: 通过XML处理propertyChange事件**

> **XML**
> 
> ---
> 
> `<Page xmlns="http://schemas.nativescript.org/tns.xsd" navigatingTo="navigatingTo">`
> 
> `<StackLayout>`
> 
> `<Switch checked="" propertyChange="onCheckChange"/>`
> 
> `</StackLayout>`
> 
> `</Page>`
> 
> **JS**
> 
> ---
> 
> `function onCheckChange(args) {`
> 
> `console.log("Property Changed!");`
> 
> `console.log("Event name:" + args.eventName);`
> 
> `console.log("Object:" + args.object);`
> 
> `console.log("propertyname:" + args.propertyName);`
> 
> `console.log("value:" + args.value);`
> 
> `}`
> 
> `exports.onCheckChange = onCheckChange;`

当 `switch` 选择的值变化时（也就是点击 switch 按钮切换时）  ，触发示例5代码片段里的 `propertyChange` 事件。

### **示例 6: 创建自定义类并继承**`Observable`**类**

> **JS**
> 
> ---
> 
> `var observableModule = require("data/observable");`
> 
> `var MyClass = (function (_super) {`
> 
> `__extends(MyClass, _super);`
> 
> `function MyClass() { _super.apply(this, arguments); }`
> 
> `Object.defineProperty(`
> 
> `MyClass.prototype,`
> 
> `"myProperty",`
> 
> `{`
> 
> `get: function () { return this._myProperty; },`
> 
> `set: function (value) { this._myProperty = value; },`
> 
> `enumerable: true,`
> 
> `configurable: true`
> 
> `}`
> 
> `); return MyClass;`
> 
> `})(observableModule.Observable);`
> 
> `exports.MyClass = MyClass;`

当属性值改变时， 触发示例6代码片段里的 `propertyChange`事件。

## **[创建自定义事件](http://docs.nativescript.org/core-concepts/events#creating-a-custom-event)**

如果你的业务逻辑需要，你可能想要在特别的行为上触发（引发或发出）一个自定义事件（参看**示例7**）。为此，当该行为完成时调用 `Obser``vable.notify()` 方法。该方法取得 [EventData 接口](http://docs.nativescript.org/api-reference/interfaces/_data_observable_.eventdata.html) 的任何实施者作为事件数据。这包括有关一个事件的基本信息—— 它的名字作为 `eventName` ，且事件发送器的一个实例作为 `object` 。

### [**示例 7: 创建自定义事件**](http://docs.nativescript.org/core-concepts/events#example-7-creating-a-custom-event)

> **JS**
> 
> ---
> 
> `var eventData = { `
> 
> `    eventName: "myCustomEventName", `
> 
> `    object: this `
> 
> `}; `
> 
> `this.notify(eventData); `

引发一个事件的最少必要信息是 `eventName` ——它将被用来执行与此事件相关联的所有事件处理器。

下一步是关联到该事件：

> `var myCustomObject = new MyClass(); `
> 
> `myCustomObject.on(`
> 
> `    "myCustomEventName", `
> 
> `    function(eventData){ console.log(eventData.eventName + " has been raised! by: " + eventData.object); }`
> 
> `)  `

A similar logic is implemented for the `propertyChange` event, so if your business logic requires that,`propertyChange` can be emitted manually through the `notify()` method \(without using the`Observable.set()` method that also fires the `propertyChange` event\). 

类似的逻辑是实现 `propertyChange` 事件，所以如果你的业务逻辑要求这样， `propertyChange` 可以手动通过 `notify()` 方法发出的（不使用 `Observable.set()` 方法也可以触发 `propertyChange` 事件）。

## [**避免内存泄漏**](http://docs.nativescript.org/core-concepts/events#avoiding-memory-leaks)

Although the radio station comparison is convenient for understanding the concept, events are a bit more complicated on the inside. To be able to notify the listener, the sender contains a pointer to the listener. Even if you set the listener object to `null` or `undefined`, it is not eligible for garbage collection, because the sender is alive and has a live reference to the listener object. This could result in a memory leak when the object lifetimes of the sender and the listener differ significantly.

Consider this scenario: A UI element creates a lot of child controls, each of which hooks to an event of the parent. Then a child control is released \(during a list view scrolling for instance\), causing a memory leak.

To prevent these memory leaks, it is a good practice to remove your event listener handler before releasing the listener object. Unfortunately, sometimes you cannot determine the exact time to call the `off` or `removeEventListener` function. In such cases, use another option of the NativeScript framework: _weak events_.

