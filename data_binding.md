数据绑定是连接用户界面 \(UI\) 到一个数据对象 \(code\) 的方法。它可以让在代码里修改的变化通过反射传递给UI，反之亦然。

> 后面的章节里， **source** 代表代码里的任何对象， **target** 代表任何UI控件（如 TextField ）。

## [**Data flow direction**](http://docs.nativescript.org/core-concepts/data-binding#data-flow-direction)数据流动方向

数据绑定的部分设置是数据流动的方向。 NativeScript 数据绑定支持如下的数据传输：

* **单向**：这是默认设置，这使得当源属性改变时目标属性更新。然而，UI修改不会更新代码并且终止绑定链接。

* **双向**：该设置使得改变的反射在两个方向——从源到目标和从目标到源。当你要处理用户输入的时候，可以使用双向数据绑定。


## [**Basic binding concepts**](http://docs.nativescript.org/core-concepts/data-binding#basic-binding-concepts)绑定的基本概念

通常，几乎每个UI控件都能绑定到一个数据对象（所有的 NativeScript 控件都是经由数据绑定创造的）。在你的代码与后面的要求匹配之后，你可以跳出盒子使用数据绑定。

* 目标对象必须是 **Bindable** 类的继承者。所有的 NativeScript 控件已经从该类继承。
* 对于双向数据绑定，目标属性应该是一个依赖属性。
* 对于单向绑定，使用一个普通属性就足够了。
* 数据对象要为其属性值里的每个变化声明一个 **propertyChange** 事件，来通知所有对变化有兴趣的监听。

## **[How to create a binding](http://docs.nativescript.org/core-concepts/data-binding#how-to-create-a-binding)**如何创建绑定

### **[Two-way binding in code](http://docs.nativescript.org/core-concepts/data-binding#two-way-binding-in-code)**在代码里双向绑定

下面的例子包含一个 `Label`，`TextField` 和绑定UI控件的source属性。这样的目的是为了，当用户在 `TextField` 输入的时候，更新代码里的属性和 `Label` 的文本。

首先， `source` 对象创建时带有一个 `textSource` 属性。从 `source` 属性向 `Label` 传递变动的固定流向是必要的。因此，代码里的属性需要声明一个 `propertyChange`  事件来通知 `Label` 改变。要声明这个事件，使用了一个内置的类，它提供这个功能—— `Observable` 。

> ### JS
> 
> ---
> 
> `var observableModule = require("data/observable");`
> 
> `var source = new observableModule.Observable();`
> 
> `source.textSource = "Text set via twoWay binding";`

然后，创建目标对象以便绑定到 `source` 属性。在这里，它们是 一个 `Label` 和一个 `TextField` ，都是继承了 `Bindable` 类的（所有的UI控件都继承了该类）。

> ### JS
> 
> ---
> 
> `//create the TextField`
> 
> `var textFieldModule = require("ui/text-field");`
> 
> `var targetTextField = new textFieldModule.TextField();`
> 
> `//create the Label`
> 
> `var labelModule = require("ui/label");`
> 
> `var targetLabel = new labelModule.Label();`

接着，目标对象绑定到 source 对象。  `TextField` 使用了一个双向绑定，所以用户输入能够改变代码里的属性。同时 `Label` 的绑定是单向的，用来只从代码向UI传递改变。

### **[Example 1: Binding label text property.](http://docs.nativescript.org/core-concepts/data-binding#example-1-binding-label-text-property)**绑定lable的文本属性

> ### JS
> 
> ---
> 
> `//binding the TextField`
> 
> `var textFieldBindingOptions = {`
> 
> `sourceProperty: "textSource",`
> 
> `targetProperty: "text",`
> 
> `twoWay: true`
> 
> `};`
> 
> `targetTextField.bind(textFieldBindingOptions, source);`
> 
> `//binding the Label`
> 
> `var labelBindingOptions = {`
> 
> `sourceProperty: "textSource",`
> 
> `targetProperty: "text",`
> 
> `twoWay: false };`
> 
> `targetLabel.bind(labelBindingOptions, source);`

### **[Binding in XML](http://docs.nativescript.org/core-concepts/data-binding#binding-in-xml)**在XML里绑定

要在XML里绑定，需要一个源对象，用同样的方式创建，像上面的例子那样（_在代码里双向绑定_）。然后在XML里描述这个绑定（使用一个小胡子语法）。在XML声明里，只设置属性名——目标：text，源: textSource 。有趣的是该绑定源不是显示地指定。关于这个主题更多的讨论会在 [Binding source](http://docs.nativescript.org/core-concepts/data-binding#binding-source) 文章里。

> `<Page>`
> 
> `<StackLayout>`
> 
> `<TextField text="{{ textSource }}" />`
> 
> `</StackLayout>`
> 
> `</Page>`
> 
> ### **注意：**
> 
> ---
> 
> **当通过XML声明创建UI元素时，数据绑定默认是双向的。**

## [**Binding source**](http://docs.nativescript.org/core-concepts/data-binding#binding-source)绑定源

### [**Binding to a property**](http://docs.nativescript.org/core-concepts/data-binding#binding-to-a-property)绑定到一个属性

设置源对象是数据绑定的一个重要部分。对于一个持续的数据流变动，目标属性需要发出一个 **propertyChange** 事件。 NativeScript 数据绑定可以和任何发出该事件的对象绑定。添加一个绑定源主要通过把它作为 **bind\(bindingOptions, source\)** 方法的第二个参数实现。当源被当作 `Bindable` 类的名为 **bindingContext** 的属性情况下，这个参数是可选和可以省略的。该属性特别之处在于它在整个视觉树是可继承的。这意味着一个UI控件可以使用它第一个父元素的 **bindingContext** ，该父元素显示地设置了 **bindingContext。**在 [Two-Way Binding in Code](http://docs.nativescript.org/core-concepts/data-binding#two-way-binding-in-code) 例子里， `bindingContext`can 可以设置到一个 `Page` 或 `StackLayout` 实例并且 `TextField` 将继承它作为一个应有的源来绑定其 "text" 属性。

> JS
> 
> ---
> 
> `page.bindingContext = source; `
> 
> `//or `
> 
> `stackLayout.bindingContext = source; `

### [**Binding to an event in XML**](http://docs.nativescript.org/core-concepts/data-binding#binding-to-an-event-in-xml)绑定到一个XML里的事件

此处有个选项来绑定一个函数在特定事件上执行（像MVVM命令）。这个选项只能通过XML声明。要实现这个功能，源对象就需要有个事件处理器函数。

### [**Example 2: Binding function on button tap event.**](http://docs.nativescript.org/core-concepts/data-binding#example-2-binding-function-on-button-tap-event)绑定函数到按钮点击事件

> XML
> 
> ---
> 
> `<Page> `
> 
> `<StackLayout> `
> 
> `<Button text="Test Button For Binding" tap="{{ onTap }}" /> `
> 
> `</StackLayout> `
> 
> `</Page> `
> 
> JS
> 
> ---
> 
> `source.set("onTap", function(eventData) { `
> 
> `console.log("button is tapped!"); `
> 
> `}); `
> 
> `page.bindingContext = source;`



> ### **注意：**
> 
> ---
> 
> 要知道如果有一个按钮在后台代码页面里具有一个事件处理器函数 **onTap** ，并且 **onTap** 函数在 **bindingContext** 对象里面，那么就不能有两个事件处理器挂载到该按钮上。要在执行后台代码里的该函数，在XML里应该使用这样的语法 - **tap="onTap"** ，而对于来自 bindingContext 的该函数则使用 **tap="{{ onTap }}"** 。

### [**Binding to a plain object**](http://docs.nativescript.org/core-concepts/data-binding#binding-to-a-plain-object)绑定到一个普通对象

通常的情况是提供一个普通元素（数字，日期，字符串）的列表（数组）到一个`ListView` 项目集合。上面的所有示例证明了如何绑定一个UI元素到 bindingContext 的属性。如果现在只有普通数据，这里就没有属性可以绑定，所以就要绑定到整个对象。这里 NativeScript 绑定的一个内容就出马了——对象或值绑定。要指向整个对象，即示例里的 Date\(\) ，就要使用关键词 `$value` 。

### [**Example 3: Bind ListView to a property of the bindingContext .**](http://docs.nativescript.org/core-concepts/data-binding#example-3-bind-listview-to-a-property-of-the-bindingcontext-)绑定 [**ListView**](http://docs.nativescript.org/core-concepts/data-binding#example-3-bind-listview-to-a-property-of-the-bindingcontext-) 到 **bindingContext** 的属性

> XML
> 
> ---
> 
> `<Page> `
> 
> `<StackLayout> `
> 
> `<ListView items="{{ items }}" height="200"> `
> 
> `<ListView.itemTemplate> `
> 
> `<Label text="{{ $value }}" /> `
> 
> `</ListView.itemTemplate> `
> 
> `</ListView> `
> 
> `</StackLayout> `
> 
> `</Page> `

> JS
> 
> ---
> 
> `var appModule = require("application"); `
> 
> `var list = []; `
> 
> `var i; for(i = 0; i < 5; i++) { `
> 
> `list.push(new Date()); `
> 
> `} `
> 
> `source.set("items", list);`

### [**Binding to a parent binding context**](http://docs.nativescript.org/core-concepts/data-binding#binding-to-a-parent-binding-context)绑定到父绑定上

绑定操作的另一个常见情况是请求访问父元素的绑定上下文。这是因为它可能不同于子节点的绑定上下文并且包含了子节点可能使用的信息。通常，绑定上下文是可继承的，但当元素\(items\)是基于某些数据源而动态创建时不是。例如，`ListView`基于一个`itemТemplate`创建它的子项目，`itemТemplate`描述`ListView`元素的外观。当这个元素添加到视觉树时，它从一个ListView `items` 数组\(通过相应的index\)获取这个元素的绑定上下文。这个进程为子项目和它的内含UI元素创建了一个新的绑定上下文链。所以，内含UI元素不能访问'ListView'的绑定上下文。为了解决这个问题，NativeScript绑定的基础架构里有两个特别的关键词：`$parent` 和 `$parents` 。第一个表示直接父视觉元素的绑定上下文，第二个可以当作数组来使用\(通过数字或字符串index\)。这就给你了选择来选择`N`级UI嵌套或是得到一个给定类型的父UI元素。我们来看看这在一个现实的例子里是如何工作的。

** XML**
---

>`<Page loaded="pageLoaded">
    <GridLayout rows="*" >
        <ListView items="{{ items }}">
            <!--Describing how the element will look like-->
            <ListView.itemTemplate>
                <GridLayout columns="auto, *">
                    <Label text="{{ $value }}" col="0"/>
                    <!--The TextField has a different bindingCotnext from the ListView, but has to use its properties. Thus the parents['ListView'] has to be used.-->
                    <TextField text="{{ $parents['ListView'].test, $parents['ListView'].test }}" col="1"/>
                </GridLayout>
            </ListView.itemTemplate>
        </ListView>
    </GridLayout>
</Page>`




### **js**
---

> `var observable = require("data/observable"); `

> `var pageModule = require("ui/page"); `

> `var viewModel = new observable.Observable(); 
`
> `function pageLoaded(args) { `

> `var page = args> .object;` 

> `viewModel.set("items", [1, 2, 3]); `

> `viewModel.set("test", "Test for parent binding!");`

> `page.bindingContext = viewModel; `

> `} `

> `exports.pageLoaded = pageLoaded;`