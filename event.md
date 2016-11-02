# **事件**

* [Overview概览](http://docs.nativescript.org/core-concepts/events#overview)
* [Adding an event handler添加一个事件处理器](http://docs.nativescript.org/core-concepts/events#adding-an-event-handler)
* [Removing an event listener移除事件监听器](http://docs.nativescript.org/core-concepts/events#removing-an-event-listener)
* [PropertyChange event属性变动 事件](http://docs.nativescript.org/core-concepts/events#propertychange-event)
* [Creating a custom event创建自定义事件](http://docs.nativescript.org/core-concepts/events#creating-a-custom-event)
* [Avoiding memory leaks避免内存泄漏](http://docs.nativescript.org/core-concepts/events#avoiding-memory-leaks)
* [Working with weak events用弱事件工作](http://docs.nativescript.org/core-concepts/events#working-with-weak-events)

## **[概览](http://docs.nativescript.org/core-concepts/events#overview)**

事件是从事件发射器发送的消息，以表示特定行为的发生。此行为可以由用户行为（如点击）或程序逻辑（例如，指示从服务器下载图像已完成）生成。引发事件的对象叫作**事件发送器**（简称**发送器**）或者**事件触发器**。消费事件的对象叫作**事件监听器**（简称**监听器**）或者**事件处理器**。

NativeScript框架提供了一个 `Observable` 类支持用事件工作的进程。有关这方面更多信息查看 [API Reference](http://docs.nativescript.org/api-reference/classes/_data_observable_.observable.html) 。因为这是NativeScript框架的基本类之一，几乎所有 NativeScript 对象（组件）都有处理事件的选项。

## **[添加事件处理器](http://docs.nativescript.org/core-concepts/events#adding-an-event-handler)**

添加事件处理器意思是设置一个函数（方法），以在事件引发的时候执行。

**示例 1** 显示了如何设置一个函数，当按钮点击的时候在控制台打印输出“Hello World”。你可以在简化语法和完整语法中选择，或是可以在XML中声明一个事件处理器。

下面的例子显示了如何用简化语法和完整语法添加一个事件处理器。这里有个可选的第三个参数代表 `this` 参数。

### **[示例1 \(JS\): 用简化和完整语法添加一个事件处理器或事件监听器](http://docs.nativescript.org/core-concepts/events#example-1-javascript-adding-an-event-handler-or-an-event-listener-using-the-short-and-full-syntax)**

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

### **[示例 1 \(XML\): 用XML声明添加事件处理器或事件监听器](http://docs.nativescript.org/core-concepts/events#example-1-xml-adding-an-event-handler-or-an-event-listener-using-an-xml-declaration)**

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

### **[示例 2: 关联按钮点击事件](http://docs.nativescript.org/core-concepts/events#example-2-hooking-to-a-button-tap-event)**

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

## **[移除事件监听器](http://docs.nativescript.org/core-concepts/events#removing-an-event-listener)**

通常你没必要移除事件监听器。当你需要只接收一次事件或者释放资源时，你可以这样做。在此情况下，你可以使用**示例3**的方法。

> ### **没有语法来通过XML声明移除事件监听器。**

### [**示例 3: 移除按钮点击事件监听器**](http://docs.nativescript.org/core-concepts/events#example-3-removing-a-button-tap-event-listener)

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

## **[属性变动事件](http://docs.nativescript.org/core-concepts/events#propertychange-event)**

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

## [**创建自定义事件**](http://docs.nativescript.org/core-concepts/events#creating-a-custom-event)

如果你的业务逻辑需要，你可能想要在特别的行为上触发（引发或发出）一个自定义事件（参看**示例7**）。为此，当该行为完成时调用 ```Obser``vable.notify()``` 方法。该方法取得 [EventData 接口](http://docs.nativescript.org/api-reference/interfaces/_data_observable_.eventdata.html) 的任何实施者作为事件数据。这包括有关一个事件的基本信息—— 它的名字作为 `eventName` ，且事件发送器的一个实例作为 `object` 。

### **[示例 7: 创建自定义事件](http://docs.nativescript.org/core-concepts/events#example-7-creating-a-custom-event)**

> **JS**
> 
> ---
> 
> `var eventData = {`
> 
> `eventName: "myCustomEventName",`
> 
> `object: this`
> 
> `};`
> 
> `this.notify(eventData);`

引发一个事件的最少必要信息是 `eventName` ——它将被用来执行与此事件相关联的所有事件处理器。

下一步是关联到该事件：

> `var myCustomObject = new MyClass();`
> 
> `myCustomObject.on(`
> 
> `"myCustomEventName",`
> 
> `function(eventData){ console.log(eventData.eventName + " has been raised! by: " + eventData.object); }`
> 
> `)`

类似的逻辑是实现 `propertyChange` 事件，所以如果你的业务逻辑要求这样， `propertyChange` 可以手动通过 `notify``()` 方法发出的（不使用 `Observable.set()` 方法也可以触发 `propertyChange` 事件）。

## **[避免内存泄漏](http://docs.nativescript.org/core-concepts/events#avoiding-memory-leaks)**

虽然无线电台的比较便于理解这个概念，但事件内部有一点复杂。为了能够通知监听器，发送器包含一个指向监听器的指针。即使你将监听器对象设置为 `null` 或 `undefined` ，它也不符合垃圾回收的条件，因为发送者是活的，并对监听器对象有一个实时引用。当发送者和听者的对象生存期存在显著差异时， 这可能会导致在内存泄漏。 

考虑这个场景：一个UI元素创建了一大堆子控件，每个都关联到一个父元素的事件。然后释放一个子控件（在列表视图滚动的情况下），会引起内存泄漏。

要避免这些内存泄漏，比较好的实践是释放监听器对象之前移除监听器的处理器。不幸的是，有时你不能确定调用 `off` 或 `removeEventListener` 函数的具体时间。在此情况下，使用 NativeScript 框架的另一个选项： _**弱事件**_ 。

## [**使用 Weak Events**](http://docs.nativescript.org/core-concepts/events#working-with-weak-events)

一个弱事件，就像其名字表露的，创建一个监听器对象的弱引用。它会帮你释放监听器对象而不用移除监听器指针。 

### [**添加弱事件监听器**](http://docs.nativescript.org/core-concepts/events#working-with-weak-events-Adding)

使用弱事件监听器和普通事件非常类似。**示例8**显示了如何添加一个弱事件监听器（为了清晰包括了代码注释）：

### [**示例 8: 创建弱事件并 处理 property change 事件**](http://docs.nativescript.org/core-concepts/events#working-with-weak-events-Example)

> **JS**
> 
> ---
> 
> `var weakEventListenerModule = require("ui/core/weak-event-listener"); `
> 
> `var buttonModule = require("ui/button"); `
> 
> `var observableModule = require("data/observable"); `
> 
> `var testButton = new buttonModule.Button(); `
> 
> `testButton.text = "Test"; `
> 
> `testButton.on(buttonModule.Button.tapEvent, function () { source.set("testProperty", "change" + counter); }); `
> 
> `var source = new observableModule.Observable(); `
> 
> `var counter = 0; `
> 
> `var handlePropertyChange = function () { counter++; this.text = counter + ""; }; `
> 
> `var weakEL = weakEventListenerModule.WeakEventListener; `
> 
> `var weakEventListenerOptions: `
> 
> `weakEventListenerModule.WeakEventListenerOptions = { `
> 
> `    // create a weak reference to the event listener object `
> 
> `    targetWeakRef: new WeakRef(this), `
> 
> `   // create a weak reference to the event sender object `
> 
> `    sourceWeakRef: new WeakRef(this.source), `
> 
> `    // set the name of the event `
> 
> `    eventName: observable.Observable.propertyChangeEvent, `
> 
> `    // set the event handler `
> 
> `   handler: handlePropertyChange, `
> 
> `    // (optional) set the context in which to execute the handler `
> 
> `    handlerContext: testButton, `
> 
> `    // (optional) set a specialized property used for extra event recognition `
> 
> `    key: this.options.targetProperty `
> 
> `} `
> 
> `weakEL.addWeakEventListener(this.weakEventListenerOptions); `

The function demonstrates how to use the `handlerContext` property—its value is taken as an argument to `this` inside the event handler function.

示例8显示了如何联接一个弱事件监听器到一个 observable 对象实例。仔细看看 `handlePropertyChange` 函数，显示了当 `propertyChange` 事件触发时（通过点击事件）， `this` 对象的 `text` 属性变化。该函数演示了如何使用 `handlerContext` 属性——在事件处理器内部，它的值被拿去当作 `this` 的一个参数。

### [**移除弱事件**](http://docs.nativescript.org/core-concepts/events#working-with-weak-events-Removing)

在事件触发调用一个函数之时， `targetWeakRef` 和 `key` 属性是可选的。然而，他们允许移除事件监听器。这些属性被用作键值对的键（key）来保存弱事件监听器。

> **JS**
> 
> ---
> 
> `weakEL.removeWeakEventListener(this.weakEventListenerOptions); `







