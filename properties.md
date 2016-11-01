# **属性**

本文包括：

* [概览](http://docs.nativescript.org/core-concepts/properties#overview)
* [依赖属性](http://docs.nativescript.org/core-concepts/properties#dependency-properties)

  * [Introduction](http://docs.nativescript.org/core-concepts/properties#intro1)
  * [Declaring a dependency property](http://docs.nativescript.org/core-concepts/properties#declaring)
  * [Adding a changed callback](http://docs.nativescript.org/core-concepts/properties#changedCallback)
  * [Adding a validation callback](http://docs.nativescript.org/core-concepts/properties#validationCallback)
  * [Creating an inheritable dependency property](http://docs.nativescript.org/core-concepts/properties#inheritable)

* [样式属性](http://docs.nativescript.org/core-concepts/properties#style-properties)

  * [Introduction](http://docs.nativescript.org/core-concepts/properties#intro2)
  * [Setting a style property](http://docs.nativescript.org/core-concepts/properties#setting)


## 概览

作为一个 TypeScript 框架， NativeScript 使用 TypeScript 属性。转译之后，这些ES5格式的结果兼容JavaScript的setter和getter方法支持类成员的工作，从而保证可读和可管理的代码。示例1中的代码演示了如何 TypeScript 转换为JavaScript。

### [**示例 1: TypeScript 如何转换成JavaScript.**](http://docs.nativescript.org/core-concepts/properties#example-1-how-typescript-is-transformed-to-javascript)

> ### TS
> 
> ---
> 
> `export class MyClass {`
> 
> `private _myProperty: number;`
> 
> `public get myProperty(): number { return this._myProperty; }`
> 
> `public set myProperty(value: number) { this._myProperty = value; }`
> 
> `}`
> 
> ### JS
> 
> ---
> 
> `var MyClass = (function () {`
> 
> `function MyClass() { }`
> 
> `Object.defineProperty(MyClass.prototype, "myProperty", {`
> 
> `get: function () { return this._myProperty; },`
> 
> `set: function (value) { this._myProperty = value; },`
> 
> `enumerable: true,`
> 
> `configurable: true`
> 
> `});`
> 
> `return MyClass;`
> 
> `})();`
> 
> `exports.MyClass = MyClass;`

TypeScript 转译器由一个grunt 脚本支持运行。

NativeScript有两种类型的属性： **dependency properties依赖属性** 和 **style properties样式属性**.。每种类型都会在后面部分介绍。

## [**依赖属性**](http://docs.nativescript.org/core-concepts/properties#dependency-properties)

### **简介**

依赖属性提供了有价值的功能，简化了创建一个丰富的UI（用户界面），包括：

* **内存优化**： 创建一个丰富的自定义用户界面控件不免要创建数量众多的属性，其中大部分都使用默认值。用传统的方法，你最终得到的每一个属性都有一个**私域**。使用依赖属性，你只需存储你修改过的实例属性。默认值存储在依赖属性中。此外，依赖属性在类外部被声明为静态，这更有助于优化内存占用。

* **值验证**：依赖属性提供业务逻辑验证。其实现是作为一个专门的验证回调函数，以**新值**为参数，并根据值有效或者无效分别返回真和假。

* **变更通知**：当属性值改变时，另一个回调函数被调用。它用一个 [EventData](http://docs.nativescript.org/api-reference/interfaces/_data_observable_.eventdata.html) 作为参数被调用。

* **继承**：依赖属性的一个最重要的特性就是继承。它是通过一个专用的UI元素来实现的，该元素允许它从视觉树中的父元素得到它的属性。例如，一个按钮可以从父窗口，布局，或其他容器继承它的样式（或主题）属性值。这给了你一个选项，通过只改变一个单一的设置（ Window.theme ），戏剧性地改变你整个应用的外观。


### **声明一个依赖属性**

只有从 [DependencyObservable](http://docs.nativescript.org/api-reference/classes/_ui_core_dependency_observable_.dependencyobservable.html) 派生的类可以拥有一个依赖属性。 这个类有内置的方法，来支持依赖属性的整个基础结构。

示例2中的代码创建了一个基础的属性，该属性参照标准属性实现，添加了一个静态部分。

### [**示例2: 如何参照标准属性创建添加l一个静态部分的属性.**](http://docs.nativescript.org/core-concepts/properties#example-2-how-to-create-property-that-adds-a-static-part-compared-to-a-standard-property)

> ### JS
> 
> ---
> 
> `var dependencyObservable = require("ui/core/dependency-observable"); `
> 
> `exports.myPropertyProperty = new dependencyObservable.Property(`
> 
> `    "myProperty", `
> 
> `    "MyClass", `
> 
> `    new dependencyObservable.PropertyMetadata("", dependencyObservable.PropertyMetadataSettings.None)`
> 
> `); `
> 
> `var MyClass = (function (_super) { `
> 
> `    __extends(MyClass, _super); `
> 
> `    function MyClass() { `
> 
> `        _super.apply(this, arguments); `
> 
> `    } `
> 
> `    Object.defineProperty(`
> 
> `        MyClass.prototype, `
> 
> `        "myProperty", `
> 
> `        { `
> 
> `            get: function () {return this._getValue(exports.myPropertyProperty); }, `
> 
> `            set: function (value) { this._setValue(exports.myPropertyProperty, value); }, `
> 
> `            enumerable: true, `
> 
> `            configurable: true `
> 
> `        }`
> 
> `    ); `
> 
> `    return MyClass; `
> 
> `})(dependencyObservable.DependencyObservable); `
> 
> `exports.MyClass = MyClass; `



