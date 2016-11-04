# 通过JS使用原生API

## 概览

NativeScript允许你从底层平台使用所有原生API。要完成这个行为，后台将发生很多事情。其中之一是 marshalling ？编组？编译？——JS与IOS的 Objective-C 数据类型和 Android 的Java数据类型之间的转换。

本文中，你将学习如何通过JS使用不同的数据类型参数来调用原生API。更多信息，参看 [iOS Runtime](http://docs.nativescript.org/runtimes/ios/marshalling/Marshalling-Overview.html) 和 [Android Runtime](http://docs.nativescript.org/runtimes/android/marshalling/overview.html) 章节的有关数据转换的具体平台资源。

# [**数字型**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#numeric-types)

当地原生的数值类型（例如，IOS的char，int，float，double 和 Android的 byte, short, int, long, double, float ）都将被隐式地转换为JavaScript的数值，反之亦然。例如，当你在IOS上运行下面的代码：

> `// iOS `
> 
> `console.log('pow(2.5, 3) = ', pow(2.5, 3)); `



iOS Runtime将转换JS数字字面量为原生的 double类型，并把它们传递给原生函数 `pow(double x, double y)` 。返回的原生整形将自动地转换为JS数字并传递给 `console.log()` 。这在 Android 上同样有效：

> `// Android `
> 
> `console.log('min(3, 4) = ', java.lang.Math.min(3, 4)); `

原生的 `java.lang.Math.min()` 要求有两个整形。 Android Runtime 知道 `java.lang.Math.min()` 的签名并将字面量3和4用JAVA整形数据类型翻译成它们的表述。返回的整形同样也自动地翻译成JS数字并传递给 `console.log()` 。

## [**类和对象**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#classes-and-objects)

所有原生的类在JS世界里由一个构造函数代表。一个原生类里的每一个静态方法在JS构造函数之上成为一个函数，且每个实例方法在JS原型之上成为一个函数。在IOS上执行这段代码：

> `// iOS `
> 
> `var array = new NSMutableArray(); `
> 
> `array.addObject(new NSObject()); `

iOS Runtime调用 `[[NSMutableArray alloc] init]` 且返回的原生对象被转换为JS对象包装器并指派为 `array1` 。该包装器拥有 `NSMutableArray` （及其前身）的原型链上的所有实例方法，所以它们可以从JS调用。为了从JS里使用更方便，方法名轻微地有所改变（比如 `tObject:atIndexedSubscript:` 在JS里命名为 `setObjectAtIndexedSubscript()`）。 Android 里同样有效：

> `// Android var context = ...; `
> 
> `var button = new android.widget.Button(context); `
> 
> `button.setText("My Button");// "My Button" is converted to java.lang.String `

# [**字符串**](http://docs.nativescript.org/core-concepts/accessing-native-apis-with-javascript#string)

JavaScript strings are implicitly marshalled to `java.lang.String` on Android and `NSString` on iOS and vice versa.

> `// iOS `
> 
> `var button = new UIButton(); `
> 
> `button.setTitleForState('Button title', UIControlStateNormal); // 'Button title' is converted to NSString `
> 
> `console.log(button.titleLabel.text); // The returned NSString is converted to JavaScript string `
> 
> ---
> 
> `// Android `
> 
> `var file = new java.io.File('myfile.txt'); // 'myfile.txt' is converted to java.lang.String `

The exception to this are the methods on `NSString` classes declared as returning `instancetype` - init methods and factory methods. This means that a call to `NSString.stringWithString` whose return type in Objective-C is `instancetype` will return a wrapper around a `NSString` instance, rather than a JavaScript string. 

Exception: Methods on `NSString` classes declared as returning `instancetype` \(e.g., init methods and factory methods\). For example, calls to `NSString.stringWithString`return `instancetype` results in Objective-C. In your NativeScript code, such calls will return a wrapper around a `NSString` instance instead of a JavaScript string. 

