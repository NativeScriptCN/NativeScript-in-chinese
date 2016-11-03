# [**Modules**](http://docs.nativescript.org/core-concepts/modules#modules)

To let you access the native device and platform capabilities of your target platform, NativeScript uses a modular design pattern. All device, platform or user interface functionalities reside in separate modules. To access the functionality provided by a module, you need to **require** the module.

In your project, the files for each module reside in a dedicated subdirectory in the `tns_modules`directory. Each default module comes along with a `package.json` file that declares how the module should be called within your call and which file contains its respective code.

