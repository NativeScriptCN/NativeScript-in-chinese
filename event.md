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

> `//Adding a listener with the short syntax简化语法 `
> 
> `var buttonModule = require("ui/button"); `
> 
> `var testButton = new buttonModule.Button(); `
> 
> `testButton.text = "Test"; `
> 
> `testButton.on(`
> 
> `    buttonModule.Button.tapEvent, `
> 
> `    function (eventData) { console.log("Hello World!"); },`
> 
> `    this`
> 
> `); `
> 
> `//Adding a lister with the full syntax完整语法 `
> 
> `var testButton2 = new buttonModule.Button(); `
> 
> `testButton2.text = "Test"; `
> 
> `var onTap = function (eventData) { console.log("Hello World!"); }; `
> 
> `testButton2.addEventListener(buttonModule.Button.tapEvent, onTap, this); `

### [**示例 1 \(XML\): 用XML声明添加事件处理器或事件监听器**](http://docs.nativescript.org/core-concepts/events#example-1-xml-adding-an-event-handler-or-an-event-listener-using-an-xml-declaration)

> `<Page> `
> 
> `    <StackLayout> `
> 
> `        <Button tap="onTap" /> `
> 
> `    </StackLayout> `
> 
> `</Page>`

你需要一个后台代码文件（看示例2）来写函数体（后台代码文件有相同的文件名，但不同的后缀：.js或.ts，取决于你使用什么语言）。

### [**示例 2: 关联按钮点击事件**](http://docs.nativescript.org/core-concepts/events#example-2-hooking-to-a-button-tap-event)

> **JS**
> 
> ---
> 
> `function onTap(eventData) { `
> 
> `    console.log("Hello World!"); `
> 
> `} `
> 
> `exports.onTap = onTap; `



> **TS**
> 
> ---
> 
> `export function onTap(eventData) { `
> 
> `    console.log("Hello World!"); `
> 
> `} `

## [**移除事件监听器**](http://docs.nativescript.org/core-concepts/events#removing-an-event-listener)

通常你没必要移除事件监听器。当你需要只接收一次事件或者释放资源时，你可以这样做。在此情况下，你可以使用**示例3**的方法。

> ### **没有语法来通过XML声明移除事件监听器。**

### [**Example 3: Removing a button tap event listener**](http://docs.nativescript.org/core-concepts/events#example-3-removing-a-button-tap-event-listener)

> **JS**
> 
> ---
> 
> `//Removing a listener with short syntax简单语法 `
> 
> `testButton.off(buttonModule.Button.tapEvent); `
> 
> `//Removing a listener with full syntax完整语法 `
> 
> `testButton2.removeEventListener(buttonModule.Button.tapEvent); `

## [**属性变动事件**](http://docs.nativescript.org/core-concepts/events#propertychange-event)

The `Observable` class provides a built-in event called `propertyChange` that is called when a property is changed. **Example 4** shows how to subscribe to this eve



