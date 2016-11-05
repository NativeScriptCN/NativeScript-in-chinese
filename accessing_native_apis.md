# 通过JS使用原生API

## 概览

NativeScript允许你从底层平台使用所有原生API。要完成这个行为，后台将发生很多事情。其中之一是 marshalling ？编组？编译？——JS与IOS的 Objective-C 数据类型和 Android 的Java数据类型之间的转换。

本文中，你将学习如何通过JS使用不同的数据类型参数来调用原生API。更多信息，参看 [iOS Runtime](http://docs.nativescript.org/runtimes/ios/marshalling/Marshalling-Overview.html) 和 [Android Runtime](http://docs.nativescript.org/runtimes/android/marshalling/overview.html) 章节的有关数据转换的具体平台资源。

## **[数字型](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#numeric-types)**

当地原生的数值类型（例如，IOS的char，int，float，double 和 Android的 byte, short, int, long, double, float ）都将被隐式地转换为JavaScript的数值，反之亦然。例如，当你在IOS上运行下面的代码：

> `// iOS`
> 
> `console.log('pow(2.5, 3) = ', pow(2.5, 3));`

iOS Runtime将转换JS数字字面量为原生的 double类型，并把它们传递给原生函数 `pow(double x, double y)` 。返回的原生整形将自动地转换为JS数字并传递给 `console.log()` 。这在 Android 上同样有效：

> `// Android`
> 
> `console.log('min(3, 4) = ', java.lang.Math.min(3, 4));`

原生的 `java.lang.Math.min()` 要求有两个整形。 Android Runtime 知道 `java.lang.Math.min()` 的签名并将字面量3和4用JAVA整形数据类型翻译成它们的表述。返回的整形同样也自动地翻译成JS数字并传递给 `console.log()` 。

## **[类和对象](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#classes-and-objects)**

所有原生的类在JS世界里由一个构造函数代表。一个原生类里的每一个静态方法在JS构造函数之上成为一个函数，且每个实例方法在JS原型之上成为一个函数。在IOS上执行这段代码：

> `// iOS`
> 
> `var array = new NSMutableArray();`
> 
> `array.addObject(new NSObject());`

iOS Runtime调用 `[[NSMutableArray alloc] init]` 且返回的原生对象被转换为JS对象包装器并指派为 `array1` 。该包装器拥有 `NSMutableArray` （及其前身）的原型链上的所有实例方法，所以它们可以从JS调用。为了从JS里使用更方便，方法名轻微地有所改变（比如 `tObject:atIndexedSubscript:` 在JS里命名为 `setObjectAtIndexedSubscript()`）。 Android 里同样有效：

> `// Android var context = ...;`
> 
> `var button = new android.widget.Button(context);`
> 
> `button.setText("My Button");// "My Button" is converted to java.lang.String`

## **[字符串](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#string)**

JavaScript 字符串在Android上隐式地整理成 `java.lang.String` ， 在IOS上是`NSString` ，反之亦然。

> `// iOS`
> 
> `var button = new UIButton();`
> 
> `button.setTitleForState('Button title', UIControlStateNormal); // 'Button title' 转换成 NSString`
> 
> `console.log(button.titleLabel.text); // 返回的 NSString 转换成 JavaScript string`
> 
> ---
> 
> `// Android`
> 
> `var file = new java.io.File('myfile.txt'); // 'myfile.txt' 转换成 java.lang.String`

这里有个例外是 `NSString` 类之上声明作为返回的 `instancetype` 的方法——初始化方法和工厂方法。这意味着对 `NSString.stringWithString` 的调用，其在 Objective-C里的返回类型是 `instancetype` ，将返回一个包装器，外面包裹着一个 `NSString` 实例，相对JS字符串而言。

> ### 例外：
> 
> 在`NSString` 类之上声明作为返回的 `instancetype` 的方法  \(如，初始化方法和工厂方法\) 。例如，对 `NSString.stringWithString` 的调用在 Objective-C 里结果是返回 `instancetype` 。在你的 NativeScript 代码里，类似的调用会返回包裹着一个 `NSString` 实例的包装器而替代JS字符串。

## [**布尔型**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#boolean)

JavaScript boolean values are implicitly marshalled to `boolean` on Android and `BOOL` on iOS and vice versa.

JS的 boolean 值隐式地整理成 Android 的 `boolean` 和IOS的 `BOOL` ，反之亦然。

> `// iOS `
> 
> `var str = NSString.stringWithString('YES'); `
> 
> `var isTrue = str.boolValue(); `
> 
> ---
> 
> `// Android `
> 
> `var str = new java.lang.String('Hello world!'); `
> 
> `var result = str.endsWith('world!'); `
> 
> `console.log(result); // true `

# [**数组**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#array)

JS数组映射为专门的 Android 上的Java数组和IOS上的 `NSArray` 。

> `// iOS `
> 
> `// nsArray is not a JavaScript array but a JavaScript wrapper around a native NSArray `
> 
> `var nsArray = NSArray.arrayWithArray(['Four', 'Five', 'Two', 'Seven']); `
> 
> `var jsArray = ['One', 'Two', 'Three'];`
> 
> ` // pure JavaScript array `
> 
> `var firstCommon = nsArray.firstObjectCommonWithArray(jsArray); `
> 
> `console.log(firstCommon); // Two `

The following code snippet shows how to call a `ns.example.Math.minElement(int[] array)` from JavaScript:

下面的代码片段显示了如何从 JS 调用一个  `ns.example.Math.minElement(int[] array)` ：

> `// Android `
> 
> `var numbers = [3, 6, 19, -2, 7, 6]; `
> 
> `var min = ns.example.Math.minElement(numbers); // -2 `

# [**Undefined & Null**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#undefined--null)

JavaScript [Undefined](http://www.w3schools.com/jsref/jsref_undefined.asp) & [Null](http://www.w3schools.com/js/js_datatypes.asp) 映射为 Java null 指针 ，而 Objective-C nil.的原生 null 值映射为 JavaScript null.

> `// iOS `
> 
> `console.log(NSStringFromClass(null)); // null`
> 
> ---
> 
> `// Android `
> 
> `var context = ...; `
> 
> `var button = new android.widget.Button(context); `
> 
> `button.setOnClickListener(undefined); // the Java call will be made using the null keyword `

# [**也可参考**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#see-also)

* [Marshalling in Android Runtime](http://docs.nativescript.org/runtimes/android/marshalling/overview.html)
* [Marshalling in iOS Runtime](http://docs.nativescript.org/runtimes/ios/marshalling/Marshalling-Overview.html)

