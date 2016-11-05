# [**Application Management应用管理**](http://docs.nativescript.org/core-concepts/application-lifecycle#application-management)

The `application` module lets you manage the life cycle of your NativeScript apps from starting the application to storing user-defined settings.

`application`模块允许你从启动应用到保存用户自定义设置整个过程进行生命周期管理。

* [Start Application启动应用](http://docs.nativescript.org/core-concepts/application-lifecycle#start-application)
* [Use Application Events使用应用行为](http://docs.nativescript.org/core-concepts/application-lifecycle#use-application-events)
* [Android Activity Events安卓活动行为](http://docs.nativescript.org/core-concepts/application-lifecycle#android-activity-events)
* [iOS UIApplicationDelegate](http://docs.nativescript.org/core-concepts/application-lifecycle#ios-uiapplicationdelegate)
* [Persist and Restore Application Settings](http://docs.nativescript.org/core-concepts/application-lifecycle#persist-and-restore-application-settings)

## [**启动应用**](http://docs.nativescript.org/core-concepts/application-lifecycle#start-application)

该方法仅适用于IOS应用。

\(_**这里有问题，app.js里靠这个方法实现主入口文件，也就是主界面定位**_\)

**IMPORTANT:** You must call the `start` method of the application module **after** the module initialization. Any code after the `start` call will not be executed. 

> ### 重要：
> 
> 你必须在 **application** 模块加载后才能调用 `start` 方法。 `start` 方法调用之后的任何代码都不会被执行。



> ### 示例
> 
> `/* iOS calls UIApplication and triggers the application main event loop. */ `
> 
> `var application = require("application"); `
> 
> `application.start({ moduleName: "main-page" }); `



## [**Use Application Events使用**](http://docs.nativescript.org/core-concepts/application-lifecycle#use-application-events) [**Application事件**](http://docs.nativescript.org/core-concepts/application-lifecycle#use-application-events)

NativeScript 应用有以下生命周期事件：

* `launch`: 此事件是在应用程序启动时引发的。
* `suspend`: 此事件是在应用程序暂停时引发的。
* `resume`: 此事件是在应用程序被暂停后恢复时引发的。
* `exit`: 此事件是在应用程序将要退出时引发的。
* `lowMemory`: 当目标设备上的内存较低时，引发此事件。
* `uncaughtError`: 当捕获应用程序一个错误时， 引发此事件。 

> ### 示例
> 
> `var application = require("application"); `
> 
> `application.on(application.launchEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `        // For Android applications, args.android is an android.content.Intent class. `
> 
> `        console.log("Launched Android application with the following intent: " + args.android + "."); `
> 
> `    } else if (args.ios !== undefined) { `
> 
> `        // For iOS applications, args.ios is NSDictionary (launchOptions). `
> 
> `        console.log("Launched iOS application with options: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.on(application.suspendEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `        // For Android applications, args.android is an android activity class. `
> 
> `        console.log("Activity: " + args.android); `
> 
> `    } else if (args.ios) { `
> 
> `        // For iOS applications, args.ios is UIApplication. `
> 
> `        console.log("UIApplication: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.on(application.resumeEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `        // For Android applications, args.android is an android activity class. `
> 
> `        console.log("Activity: " + args.android); `
> 
> `    } else if (args.ios) { `
> 
> `        // For iOS applications, args.ios is UIApplication. `
> 
> `        console.log("UIApplication: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.on(application.exitEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `       // For Android applications, args.android is an android activity class. `
> 
> `        console.log("Activity: " + args.android); `
> 
> `    } else if (args.ios) { `
> 
> `        // For iOS applications, args.ios is UIApplication. `
> 
> `        console.log("UIApplication: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.on(application.lowMemoryEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `        // For Android applications, args.android is an android activity class. `
> 
> `        console.log("Activity: " + args.android); `
> 
> `    } else if (args.ios) { `
> 
> `        // For iOS applications, args.ios is UIApplication. `
> 
> `        console.log("UIApplication: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.on(application.uncaughtErrorEvent, function (args) { `
> 
> `    if (args.android) { `
> 
> `        // For Android applications, args.android is an NativeScriptError. `
> 
> `        console.log("NativeScriptError: " + args.android); `
> 
> `    } else if (args.ios) { `
> 
> `        // For iOS applications, args.ios is NativeScriptError. `
> 
> `        console.log("NativeScriptError: " + args.ios); `
> 
> `    } `
> 
> `}); `
> 
> `application.start({ moduleName: "main-page" }); `

## [**Android 活动事件**](http://docs.nativescript.org/core-concepts/application-lifecycle#android-activity-events)

NativeScript 应用程序具有以下特定于安卓的行为事件：

* `activityCreated`: 当行为创建时引发该事件。
* `activityDestroyed`:  当行为销毁时引发该事件。 
* `activityStarted`:  当行为开始时引发该事件。 
* `activityPaused`:  当行为暂停时引发该事件。 
* `activityResumed`:  当行为恢复时引发该事件。 
* `activityStopped`:  当行为停止时引发该事件。 
* `saveActivityState`:该事件在进程被kill之前引发来恢复每个实例的状态，所以state是可以恢复的。
* `activityResult`: This event is raised when an activity you launched exits, giving you the requestCode you started it with, the resultCode it returned, and any additional data from it.这句很晦涩啊——当你运行一个存在的活动，……
* `activityBackPressed`: 当行为检测到用户按下返回键时引发该事件。

> ### 示例
> 
> `var application = require("application"); `
> 
> `if (application.android) { `
> 
> `    application.android.on(application.AndroidApplication.activityCreatedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity + ", Bundle: " + args.bundle); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityDestroyedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityStartedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityPausedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityResumedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityStoppedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.saveActivityStateEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity + ", Bundle: " + args.bundle); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityResultEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity + ", requestCode: " + args.requestCode + ", resultCode: " + args.resultCode + ", Intent: " + args.intent); `
> 
> `    }); `
> 
> `    application.android.on(application.AndroidApplication.activityBackPressedEvent, function (args) { `
> 
> `        console.log("Event: " + args.eventName + ", Activity: " + args.activity); // Set args.cancel = true to cancel back navigation and do something custom. `
> 
> `    }); `
> 
> `} `
> 
> `application.start(); `

### [**iOS UIApplicationDelegate\(UI应用委托\)**](http://docs.nativescript.org/core-concepts/application-lifecycle#ios-uiapplicationdelegate)

从 NativeScript 1.3 起，你可以为IOS应用指定自定义的 UIApplicationDelegate：

> ### 示例
> 
> `var application = require("application"); `
> 
> `var MyDelegate = (function (_super) { `
> 
> `    __extends(MyDelegate, _super); `
> 
> `    function MyDelegate() { `
> 
> `        _super.apply(this, arguments); `
> 
> `    } `
> 
> `    MyDelegate.prototype.applicationDidFinishLaunchingWithOptions = function (application, launchOptions) { `
> 
> `        console.log("applicationWillFinishLaunchingWithOptions: " + launchOptions); return true; `
> 
> `    }; `
> 
> `    MyDelegate.prototype.applicationDidBecomeActive = function (application) { `
> 
> `        console.log("applicationDidBecomeActive: " + application); `
> 
> `    }; `
> 
> `    MyDelegate.ObjCProtocols = [UIApplicationDelegate]; `
> 
> `    return MyDelegate; `
> 
> `})(UIResponder); `
> 
> `application.ios.delegate = MyDelegate; `
> 
> `application.start(); `

### [**保留和恢复应用设置**](http://docs.nativescript.org/core-concepts/application-lifecycle#persist-and-restore-application-settings)

要保留用户定义的设置，你需要使用 `application-settings` 模块。该模块是一个静态的单列的哈希表，用于存储应用程序的键值对。 getter 方法有两个参数：一个key，和一个可选的默认value，以便在指定的key不存在时返回该value。 setter 方法要求有两个参数： key 和 value 。

> ### 示例
> 
> `var applicationSettings = require("application-settings"); `
> 
> `// Event handler for Page "loaded" event attached in main-page.xml. `
> 
> `function pageLoaded(args) { `
> 
> `    applicationSettings.setString("Name", "John Doe"); `
> 
> `    console.log(applicationSettings.getString("Name")); `
> 
> `    // Prints "John Doe". `
> 
> `    applicationSettings.setBoolean("Married", false); `
> 
> `    console.log(applicationSettings.getBoolean("Married")); `
> 
> `    // Prints false. `
> 
> `    applicationSettings.setNumber("Age", 42); `
> 
> `    console.log(applicationSettings.getNumber("Age")); `
> 
> `    // Prints 42. `
> 
> `    console.log(applicationSettings.hasKey("Name")); `
> 
> `    // Prints true. `
> 
> `    applicationSettings.remove("Name"); `
> 
> `    // Removes the Name entry. `
> 
> `    console.log(applicationSettings.hasKey("Name")); `
> 
> `    // Prints false. `
> 
> `} `
> 
> `exports.pageLoaded = pageLoaded; `

