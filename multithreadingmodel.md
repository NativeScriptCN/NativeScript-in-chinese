# [**多线程模式**](http://docs.nativescript.org/core-concepts/multithreading-model#multithreading-model)

NativeScript的好处之一就是它允许通过JS快速高效地使用所有原生平台（ Android\/Objective-C ）的API，而不用使用串行化或者映射。然而这导致另一个纠结——所有JS都在主线程上执行 \(又名： `UI thread`\) 。这意味着操作很可能花掉很多时间停滞于UI渲染并且让应用看起来感觉很慢。

要解决UI的清晰度和高性能带来的缓慢是至关重要的，开发者可以使用 NativeScript的解决方案处理多线程——工作者线程。工作者是在一个绝对隔离的上下文中在后台线程上执行的脚本。要花掉很多时间执行的任务应该卸装到一个工作者线程之上。

NativeScript 里的 Workers  API 是松散地基于 [Dedicated Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 和 [Web Workers Specification](https://www.w3.org/TR/workers/) 。

* [Workers API](http://docs.nativescript.org/core-concepts/multithreading-model#workers-api)

  * [Worker Object](http://docs.nativescript.org/core-concepts/multithreading-model#worker-object-prototype)
  * [Worker Global Scope](http://docs.nativescript.org/core-concepts/multithreading-model#worker-global-scope)

* [Sample Usage](http://docs.nativescript.org/core-concepts/multithreading-model#sample-usage)

* [Limitations](http://docs.nativescript.org/core-concepts/multithreading-model#limitations)


## [**Workers API**](http://docs.nativescript.org/core-concepts/multithreading-model#workers-api)

### **[Worker 对象原型](http://docs.nativescript.org/core-concepts/multithreading-model#worker-object-prototype)**

* `new Worker(path)` - 创建一个worker的一个实例，并生成一个操作系统的新线程，该脚本的指向由执行的 `path` 参数决定。
* `postMessage(message)` - 发送一个串行化的JSON消息关联脚本的 `onmessage` 事件处理器。
* `terminate()` - 终止运行下一个运行循环上的worker线程。

**Worker** 对象事件处理器

* `onmessage(message)` - 处理发送自关联的worker线程传入的消息。消息对象有如下属性：

  * `message.data` - 消息内容， 如在worker线程发送的 `postMessage` 。

* `onerror(error)` - 处理从worker线程未捕获的错误。. 错误对象暴露如下属性：

  * `error.message` - 未发现的错误，一个堆栈，如果可用的话。
  * `error.filename` - 文件位置，如果抛出未捕获错误的话。
    `error.lineno` -  行号，如果抛出未捕获错误的话。 


### **[Worker 全局范围             ](http://docs.nativescript.org/core-concepts/multithreading-model#worker-global-scope)**

* `self` - 返回 `WorkerGlobalScope` 本身的一个引用。
* `postMessage(message)` - 发送一个串行化的JSON消息到主线程上的Worker 实例的 `onmessage` 事件处理器。
* `close()` -  终止运行下一个运行循环上的worker线程。 

**Worker 全局范围事件处理器**

* `onmessage(message)` - 处理发送自主r线程传入的消息。消息对象暴露如下属性：

  * `message.data` -  消息内容， 如在主r线程发送的 `postMessage` 。

* `onerror(error)` - 处理 Worker 作用域（worker线程）内部执行的函数未捕获的错误 。 `error` 参数包含未捕获的错误。如果处理器返回一个真类的值，消息不会传播到主线程上的 Worker 实例的 `onerror` 处理器。`onerror` 调用 worker 线程之后，执行不会被终止，worker仍然能够发送\/接收消息。

* `onclose()` - 处理任何“清理”工作；适用于释放资源，关闭流和套接字。


![](/assets/Workers1.png)

> **main-view-model.js **
> 
> ---
> 
> `...`
> 
> `var worker = new Worker('./workers/image-processor');`
> 
> `worker.postMessage({ src: imageSource, mode: 'scale', options: options });`
> 
> `worker.onmessage = function(msg) {`
> 
> `if (msg.data.success) {`
> 
> `// Stop idle animation`
> 
> `// Update Image View`
> 
> `// Terminate worker or send another message`
> 
> `worker.terminate();`
> 
> `} else {`
> 
> `// Stop idle animation`
> 
> `// Display meaningful message`
> 
> `// Terminate worker or send message with different parameters`
> 
> `}`
> 
> `}`
> 
> `worker.onerror = function(err) {`
> 
> ``console.log(`An unhandled error occurred in worker: ${err.filename}, line: ${err.lineno} :`);``
> 
> `console.log(err.message);`
> 
> `}`
> 
> `...`
> 
> **workers\/image-processor.js **
> 
> ---
> 
> `onmessage = function(msg) {`
> 
> `var request = msg.data;`
> 
> `var src = request.src;`
> 
> `var mode = request.mode || 'noop' ;`
> 
> `var options = request.options;`
> 
> `var result = processImage(src, mode, options);`
> 
> `var msg = result !== undefined ? { success: true, src: result } : { }`
> 
> `postMessage(msg);`
> 
> `}`
> 
> `function processImage(src, mode, options) {`
> 
> `// image processing logic`
> 
> `// save image, retrieve location`
> 
> `// return source to processed image`
> 
> `return updatedImgSrc;`
> 
> `}`
> 
> ``// does not handle errors with an `onerror` handler``
> 
> `// errors will propagate directly to the main thread Worker instance`

## [**General Guidelines**](http://docs.nativescript.org/core-concepts/multithreading-model#general-guidelines)通用指导原则

为了最理想的结果，当使用 Workers API 时，请遵循这些准则：

* 当 worker结束任务时，使用适合的API \(`terminate()` 或`close()`\) ，始终要确保关闭 worker 线程。如果在你工作的作用域之内，你的 Worker 实例在你能终止它之前变得不能得到，你要关闭它，就只能在worker自身的脚本里通过调用 `close()` 函数。
* Workers 不是所有性能相关问题的通用解决方案。 开启一个Worker 有它自身的开销， 而且它可能有时会比处理一个快速任务更慢。所以，在诉诸worker之前优化数据库请求，或重新考虑复杂应用逻辑。
* 线程可以访问整个原生SDK， 当 调用API时 ，nativescript 开发者必须照顾到所有的同步，这些从多个线程的调用无法保证线程安全。

## **[Limitations限制](http://docs.nativescript.org/core-concepts/multithreading-model#limitations)**

当使用 worker时有一些限制要记住：

* 不存在JS共享内存。这意味着你不能从任一线程获得JS的值\/对象。你只能串行化对象，把它发送到另一个线程并在那里反串行化。这就是 postMessage\(\) 函数所负责的。然而，原生对象没有这个情况。你可以从多个线程获取一个原生对象，而不用复制它，因为 runtime 将创建每个线程单独的JavaScript包装对象。请记住，当您使用非线程安全的原生API和数据时，你必须自己处理同步部分。 runtime 在原生数据访问和API调用上，不执行任何锁定或者同步逻辑。
* 只有串行化JSON 对象可以被 postMessage\(\) API 发送。

  * 你不能发送原生对象。意思是你不能通过 postMessage 发送原生对象，因为在大多数情况下，JSON序列化JavaScript包装一个原生对象的结果为空的-“{ }” 字面的对象。另一方面这个消息会被反序列化到一个纯粹的空的JavaScript对象。发送原生对象是我们希望在未来支持的，所以敬请关注。
  * 此外，当发送循环对象时要小心，因为它们的递归节点将在序列化步骤中剥离。

*  没有对象传输。如果你是一个Web开发者你可能熟悉ArrayBuffer和MessagePort的传输在浏览器被支持。目前， NativeScript 没有这种对象传输的概念。

* 目前，你无法调试运行在worker线程的上下文中的脚本。以后会实现。

* 没有嵌套的worker支持。我们希望从社区获知如果这在一定程度上需要我们支持的话。

