# 介绍

Objective-C语言从编译时和链接时推迟尽可能多的决定到运行时。只要有可能，它会动态的处理事务。这意味着语言不仅需要一个编译器，也需要一个运行时系统去执行编译后的代码。运行时系统充当Objective-C语言的一种操作系统；这正是语言发挥作用的原因。

这个文档观察NSObject类和Objective-C程序如何与运行时系统交互。尤其，它检查了运行时动态加载新类并转发消息到其他对象的机制。也提供了关于在程序运行时如何发现关于对象的信息的信息。

你应该阅读这个文档以了解Objective-C运行时系统工作原理以及你如何利用它。但是，通常情况下，去写一个Cocoa程序，你应该没有理由需要了解和理解这个材料来编写一个Cocoa应用程序。

## 本文档的组织

这个文档有下面这些章节：

* 运行时版本和平台（[Runtime Versions and Platforms](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtVersionsPlatforms.html#//apple_ref/doc/uid/TP40008048-CH106-SW1)）
* 与运行时交互（[Interacting with the Runtime](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtInteracting.html#//apple_ref/doc/uid/TP40008048-CH103-SW1)）
* 消息（[Messaging](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)）
* 动态方法解析（[Dynamic Method Resolution](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html#//apple_ref/doc/uid/TP40008048-CH102-SW1)）
* 消息转发
* 类型编码
* 声明的属性

## 也可以看看

[_Objective-C Runtime Reference_](https://developer.apple.com/documentation/objectivec/objective_c_runtime)描述了Objective-C运行时支持的库的数据结构和函数。你的程序可以使用这些接口跟Objective-C运行时系统交互。例如，你可以添加类或方法，或获取加载类的所有类定义的列表。

[_Programming with Objective-C_](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011210)描述Objective-C语言。

[_Objective-C Release Notes_](https://developer.apple.com/library/content/releasenotes/Cocoa/RN-ObjectiveC/index.html#//apple_ref/doc/uid/TP40004309)描述最近发布的OS X中的一些Objective-C运行时的改动。



