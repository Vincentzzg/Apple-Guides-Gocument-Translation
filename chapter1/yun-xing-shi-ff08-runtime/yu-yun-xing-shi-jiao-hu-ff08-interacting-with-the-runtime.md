# 与运行时交互（Interacting with the Runtime）

Objective-C程序在三个不同的层次上与运行时系统的交互：通过Objective-C源代码；通过基础框架中的NSObject类中定义的方法；通过直接调用运行时函数。

## Objective-C源码（Objective-C Source Code）

大部分情况下，运行时系统自动并在幕后工作。你使用它仅仅通过编写和编译Objective-C源码。

当你编译包含Objective-C类和方法的代码，编译器创建创建实现语言动态特性的数据结构和函数调用。数据结构捕获在类和分类定义和协议声明中发现的信息；它们包括在_The Objective-C Programming Language_中定义类和协议（Defining a Class and Protocols）中讨论的类和协议对象，以及方法选择器，实例变量模板和从源代码中提取的其他信息。主要的运行时函数是发送消息的那个，像[Messaging](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)中描述的一样。它由源代码消息表达式调用。

## NSObject方法（NSObject Methods）

## 运行时函数（Runtime Functions）



