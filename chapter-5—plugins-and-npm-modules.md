# **Chapter 5—Plugins and npm Modules**

# 插件和npm模块

As you build more complex apps, you'll likely run into functionality that is not implemented in the NativeScript modules. But no worries, as NativeScript lets you leverage [npm](https://www.npmjs.com/) \(node package manager\) to import npm modules into your apps. Alternately, you can install NativeScript plugins, which are simply npm modules that can access native code and use Android and iOS SDKs, if required.

In this chapter, you'll install and use an external email validator module to verify the format of email addresses as they are entered on the registration screen. Then, you'll add a NativeScript plugin,[NativeScript social share](https://www.npmjs.com/package/nativescript-social-share), to let users share their grocery lists using their device's native sharing widget.

## **Table of contents**

* [5.1: Using npm modules](#5-1)
* 5.2: Using NativeScript plugins

## [**5.1: Using npm modules**](http://docs.nativescript.org/tutorial/chapter-5#51-using-npm-modules)使用npm模块 {#5-1}

It would be nice to be able to make sure people are entering well-formatted email addresses into your app on the registration screen. You could write this functionality yourself, but validating email addresses is [surprisingly tricky](http://stackoverflow.com/questions/46155/validate-email-address-in-javascript), and it's a lot easier to use one of many npm modules that already provide this validation. For Groceries let's see how to add this [email-validator module](https://www.npmjs.com/package/email-validator) to test for valid addresses.

test edit

