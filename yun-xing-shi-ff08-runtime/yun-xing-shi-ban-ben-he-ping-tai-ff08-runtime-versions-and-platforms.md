# 运行时版本和平台（Runtime Versions and Platforms）

不同的平台上有不同的Objective-C运行时版本。

## 传统和现代版本（Legacy and Modern Versions）

Objective-C运行时有两个版本--“现代”和“传统”。Objective-C2.0引入了现代版本，并包含了许多新特性。Objective-C 1运行时参考中描述了传统版本运行时编程接口；[_Objective-C Runtime Reference_](https://developer.apple.com/documentation/objectivec/objective_c_runtime)中描述了现代版运行时编程接口。

最显著的新特性是现代版运行时中实例变量是“非易碎”的：

* 在传统运行时中，如果你改变了一个类中实例变量的布局，你必须重新编译继承自它的类。
* 在现代运行时中，如果你改变了一个类中实例变量的布局，你不需要重新编辑继承自它的类。

此外，现代运行时支持实例变量合成声明属性（请参阅Objective-C编程语言中的声明属性）。

## 平台（Platforms）

iPhone应用程序和OS X v10.5及更高版本上的64位程序使用现代版本的运行时。

其他程序（OS X桌面上的32位程序）使用运行时的旧版本。

