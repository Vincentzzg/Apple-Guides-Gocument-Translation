# 与运行时交互（Interacting with the Runtime）

Objective-C程序在三个不同的层次上与运行时系统的交互：通过Objective-C源代码；通过基础框架中的NSObject类中定义的方法；通过直接调用运行时函数。

## Objective-C源码（Objective-C Source Code）

大部分情况下，运行时系统自动并在幕后工作。你使用它仅仅通过编写和编译Objective-C源码。

当你编译包含Objective-C类和方法的代码，编译器创建实现语言动态特性的数据结构和函数调用。数据结构捕获在类和分类定义和协议声明中发现的信息；它们包括在_The Objective-C Programming Language_中定义类和协议（Defining a Class and Protocols）中讨论的类和协议对象，以及方法选择器，实例变量模板和从源代码中提取的其他信息。主要的运行时功能是发送消息的功能，像[Messaging](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)中描述的一样。它由源代码消息表达式调用。

## NSObject方法（NSObject Methods）

Cocoa中的大部分对象是NSObject类的子类，所以大部分对象继承了它定义的方法。（显著的异常是NSProxy类，更多信息请参阅[Message Forwarding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)。）因此，它的方法建立了每个实例和每个类对象所固有的行为。然而，少数情况下，NSObject类仅仅定义了了一个模板，用于如何完成某些工作；它不提供所有必须要的代码本身。

例如，NSObject类定义了一个返回一个描述类的内容的字符串的description实例方法。这主要用于调试--GCD print-object命令打印该方法返回的字符串。NSObject这个方法的实现不知道类包含什么，所以它返回一个对象名称和地址的字符串。NSObject的子类可以实现这个方法去返回更多细节。例如，基础类NSArray返回它包含的对象描述的列表。

一些NSObject方法简单的像运行时系统查询信息。这些方法允许对象执行自省。这些方法的例子是类（class）方法，它要求一个对象来识别它的类；isKindOfClass:和isMemberOfClass:，测试一个对象在继承层次结构中的位置；respondsToSelector:指明是否一个对象可以接收一个特定的消息；conformsToProtocol:,指明是否一个对象声明实现在一个特殊协议中定义的方法；methodForSelector:提供一个方法实现的地址。像这些方法给一个对象自省自己的能力。

## 运行时函数（Runtime Functions）



