注\* NativeScript是最近推出的一个跨平台解决方案，可以让你可以用JavaScript来直接写Android、iOS本地应用程序，未来还即将扩展到Windows平台。是最近比较受关注的项目。它与[nw](https://github.com/nwjs/nw.js/)\(原名node-webkit ，用Web写winodw/linux桌面应用\)和[phonegap](http://phonegap.com/)内嵌webview写APP的实现方式有着本质的不同，它直接用JavaScript调用系统原生API，因而有一些原生应用的特点。

### NativeScript

NativeScript是一个运行环境，可以让你使用通用的JavaScript代码，打造原生的iOS，Android和Windows（即将推出）应用程序。 NativeScript有很多很酷的功能，比如支持JavaScript对象双向绑定到原生UI组件，以及用CSS为原生应用程序写样式。但我最喜欢的功能是NativeScript可以让您直接访问本地平台的原生API。

注\* 可以理解为NativeScript是一个JavaScript V8运行环境的命令转发代理，将JavaScript调用转发给不同平台上的原生API如Android、iOS，以及即将支持的Windows。

例如，看看这个NativeScript写的Android应用程序的代码：

```
var time = new android.text.format.Time();
time.set( 1, 0, 2015 );
console.log( time.format( "%D" ) );
```

你只需要一两分钟来分析一下就明白了，这段JavaScript代码实例化一个Java android.text.format.Time\(\)对象，调用其set\(\)方法，然后打印format后的返回值，是字符串“01/01/15”。

我知道你已经很激动了，先不要慌，让我们再来看看iOS的代码：

```
var alert = new UIAlertView();
alert.message = "Hello world!";
alert.addButtonWithTitle( "OK" );
alert.show();
```

这段JavaScript代码实例化一个Objective-C UIAlertView类，设置它的信息属性，然后调用它的addButtonWithTitle\(\)和show\(\)方法。当您运行这段代码，你会看到hello word的警告框。

### NativeScript运行时

该NativeScript运行环境看起来可能像变魔术一样，不管你信不信，该架构并不是那么的复杂。一切从JavaScript虚拟机开始，NativeScript从这里开始执行JavaScript指令。具体来说，NativeScript在Android采用v8；在iOS上采用[JavaScriptCore](http://trac.webkit.org/wiki/JavaScriptCore)。由于NativeScript使用JavaScript虚拟机，你访问原生API的所有JavaScript代码，仍然需要遵守JavaScript的语法结构和规范。

一般来说，NativeScript会同时采用V8和JavaScriptCore的最新稳定版;因此NativeScript对ECMAScript语言的支持在iOS的桌面Safari上面几乎是相同的，并且NativeScript在Android上面也几乎与桌面浏览器相同。包括一些ES6的新语法。

了解NativeScript使用了JavaScript虚拟机是很重要的，但这只是实现的第一步，让我们来看看上文示例的第一行代码：

```
vartime = new android.text.format.Time();
```

在NativeScript Android运行环境中，该代码会被编译（[JIT](http://thibaultlaurens.github.io/javascript/2013/04/29/how-the-v8-engine-works/)）并在V8中执行。这我们可能会很容易理解变...：

```
var x = 1 + 2;
```

但是接下来的问题是...V8怎么知道什么是android.text.format.Time\(\)呢？

我们将重点讲V8和Android的实现，基本架构模式同样适用于iOS上的JavaScriptCore。如果出现显着差别，本文会提到。

这里将不讨论NativeScript在Windows上的实现细节，因为解决方案可能还会变，但是，当前的Windows实现跟iOS上的JavaScriptCore运行原理几乎一样。

### NativeScript是怎样管理JavaScript虚拟机的

V8知道android是什么，因为NativeScript在运行时进行了注入，因为V8拥有一堆让你配置JavaScript环境的API。在JavaScript中您可以使用自定义的C++代码来分析CPU使用率，管理的JavaScript垃圾收集，等等一大堆API

面对这些API的是几个“Context”类，可以让你操纵全局变量，从而有可能为NativeScript注入一个全局的android对象。这实际上使用了与Node.js的相同运行机制，使全局API可用 - 如 require\(\) - NativeScript使用它注入可以让你访问本地代码的API。 JavaScriptCore的也有类似的机制。酷吧？

让我们回到我们的代码：

```
vartime = new android.text.format.Time();
```

现在你知道这个代码在V8中运行时，而V8已经知道什么是android.text.format.Time\(\)了，因为NativeScript注入了必需的对象到全局范围。但仍存在着一些大的悬而未决的问题，如何让NativeScript明白那些注入的API到底是干什么的，然后调用？

### Metadata（元数据）

该NativeScript运行环境看起来可能像变魔术一样，不管你信不信，该架构并不是那么的复杂。一切从JavaScript虚拟机开始，NativeScript从这里开始执行JavaScript指令。具体来说，NativeScript在Android采用v8；在iOS上采用[JavaScriptCore](http://trac.webkit.org/wiki/JavaScriptCore)。由于NativeScript使用JavaScript虚拟机，你访问原生API的所有JavaScript代码，仍然需要遵守JavaScript的语法结构和规范。

对于NativeScript，反射是让NativeScript可以调用每个平台上的API的基石。包括android.text.format.Time。因为从性能角度来看重构这些API是很困难的，NativeScript会提前做掉这些，并在Android/iOS预编绎过程中嵌入预先生成的元数据。

考虑到这一点，让我们再次回到我们的代码：

```
vartime = new android.text.format.Time();
```

现在你了解了这个V8代码是这样运行的，即NativeScript注入了android.text.format.Time的JavaScript对象，通过每一个单独的元数据注入。下一个问题：如何将NativeScript里的JavaScript调用Time\(\)转发到本机android.text.format.Time\(\)？

### 调用本地代码

NativeScript如何调用本机代码的答案就在于JavaScript虚拟机的API。我们上次使用V8的API是注入全局变量。这一次，我们将着眼于在JavaScript回调中调用给定的C++代码。

例如，JavaScript函数调用的代码 new android.text.format.Time\(\)，V8会产生一个回调。也就是说V8有一个回调，让NativeScript拦截函数调用，然后用自定义的C ++代码执行一些动作，并返回一 个新的结果。

在Android中的情 况下，NativeScript运行的C++代码不能直接访问Java API，如android.text.format.Time。然而，Android的[JNI](http://developer.android.com/training/articles/perf-jni.html)，或Java本地接口，提供了C++和Java之间的桥接能力，所以NativeScript使用JNI完成转发。在iOS中这个桥梁是不必要的，因为C++代码可以直接调用Objective-C的API。

了解了这些，让我们再回到代码：

```
vartime = new android.text.format.Time();
```

我们已经知道，这个代码在V8中运行;是因为NativeScript注入过对象，它知道什么是android.text.format.Time;并且NativeScript中有这些基于元数据生成的API。我们现在知道，当 Timer\(\) 执行时，会发生下面的事情：

1）V8运行回调函数。

2）NativeScript运行时通过它的元数据知道，Time\(\)调用需要实例化一个android.text.format.Time对象。

3）NativeScript运行时使用JNI来实例化一个android.text.format.Time对象并保持对它的引用。

4）NativeScript运行时将代理的Java Time对象转化成JavaScript对象返回。

5）控制返回的JavaScript代理对象为被存储起来的本地时间变量。

代理对象是在NativeScript中保持JavaScript对象与本地平台对象的人工映射。例如，让我们来看看前面代码的下一行：

```
var time = new android.text.format.Time();
time.set( 1, 0, 2015 );
```

基于所生成的元数据，NativeScript知道代理对象上的所有方法。在这种情况下，代码调用Timer对象的set\(\)方法时。此方法会再次调用V8及其功能回调; 然后NativeScript通过Android JNI转发到Java时间对象上相应的方法调用。

这就是NativeScript在部分的工作原理。酷吧？

现在，还遗留下了一些非常复杂的部分，因为将Objective-C和Java对象转换成JavaScript对象可能会很麻烦，尤其是考虑到不同的继承类型语言时。

我们不打算深入探讨这些问题的细节，NativeScript的另一个特点让你不必深入到本地代码，比如：TNS模块。

### TNS Modules

TNS modules是Telerik NativeScript modules的简写。跟Node模块一样，它同样使用CommonJS。因此如果你已经会用require\(\)和exports对象，那么你就已经掌握了TNS模块。

TNS模块允许你将特定的本地调用抽象成平台无关的API，NativeScript本身提供了几十个这样的模块供您使用。举个例子，假设您需要在您的iOS / Android应用程序创建的文件。你可能在Android中要写以下代码：

```
newjava.io.File( path );
```

同样在iOS里写下面的代码：

```
NSFileManager.defaultManager();
fileManager.createFileAtPathContentsAttributes( path );
```

但是如果你使用TNS文件系统模块，你的代码将是一样的，而不必担心的iOS / Android的内部实现细节：

```
var fs = require( "file-system" );
var file = new fs.File( path );
```

更酷的是，你可以自己写TNS模块。比如这里有一个TNS模块，检索设备的操作系统版本：

```
// device.ios.js
module.exports = {
    version: UIDevice.currentDevice().systemVersion
}

// device.android.js
module.exports = {
    version: android.os.Build.VERSION.RELEASE
}
```

此代码只检索一个版本属性，但它给你多少灵感？使用自定义TNS模块是微不足道的，跟在nodejs中使用NPM模块一样：

```
var device = require( "./device" );
console.log( device.version );
```

如果你已经熟悉了npm的使用，NativeScript模块非常容易编写，分发和使用。就个人而言，作为一个Web开发人员，原生的iOS和Android代码让我害怕，尤其是当Java / Objective-C的API文档扔在一起的功能，它降低了我们跨平台开发的障碍。



译文转至：http://ourjs.com/detail/550138f51e8c708516000005

原文地址：http://developer.telerik.com/featured/nativescript-works

