# 语言

## 关于Objective-C

Objective-C是iOS开发的主要语言。它是C语言的超集，提供面向对象的功能和动态运行时。Objective-C继承了C的语法，基本类型和流控制语句，并添加了定义类和方法的语法。它还为对象图管理和推向字面值提供了语言级别的支持，同时提供动态的键入和绑定，将许多责任推迟到运行时。

> IDE（Integrated Development Environment）：集成开发环境，是用于提供程序开发环境的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面等工具。集成了代码编写功能、分析功能、编译功能、调试功能等一体化的开发软件服务套。

## Cocoa（Touch）

Cocoa和Cocoa Touch分别是OS X和iOS的应用程序开发环境。Cocoa和Cocoa Touch都包含了Objective-C运行时和两个核心框架：

* Cocoa，包含Foundation库和AppKit库，用于开发OS X系统上运行的应用程序
* Cocoa Touch，包含Foundation和UIKit库，用于开发运行在iOS系统上的应用程序。

> 注意：术语“Cocoa”一般用于指基于Objective-C运行时并且从根类NSObject继承任何类或对象。当应用程序开发使用相应平台的任何编程接口时，也是用术语“Cocoa”或“Cocoa Touch”。



### 框架（Frameworks）

Foundation框架实现了定义对象基础行为的根类NSObject。它实现了代表基本类型（例如，字符串和数字）和集合的（例如，数组和字典）的类。Foundation还提供了国际化，对象持久性，文件管理和XML处理的能力。你可以使用它里面的类访问底层的系统实体和服务，例如端口，线程，锁和进程。Foundation基于Core Foundation框架，该框架发布了一个程序（ANSI C）接口。

你可以使用AppKit或UIKit框架开发应用程序的用户界面。这两个框架其实是一样的，只是分别针对不同的特定平台。他们包含很多功能类，例如事件处理，绘图，图像处理，文字处理，排版和交互应用程序数据传输。它们也包含用户界面元素，例如表格视图（table views），滑块（sliders），按钮（buttons），文本字段（text fields）和警报对话框（alert dialogs）。



### 语言（Language）

Objective-C是本地的，Cocoa和Cocoa Touch应用程序开发的主要语言。然而，Cocoa和Cocoa Touch的应用程序工程中也可以包含C++和ANSI C的代码。另外，你也可以使用桥接到Objective-C运行时的脚本语言，例如PyObjcC和RubyCocoa，来开发Cocoa应用程序。



### **给对象增加成员变量**

通过**objc\_setAssociatedObject**和**objc\_getAssociatedObject**方法可以变相的给对象增加成员变量（机制不同，没有修改对象的内存结构）

**Method Swizzling（方法交换）**  
Objective-C里面提供可以**动态替换类方法或实例方法的方法**：

* class\_replaceMethod 替换类方法的定义
* method\_exchangeImplementations 交换两个方法的实现
* method\_setImplementation 设置一个方法的实现

## 动态特性（！没怎么理解）

1、动态类型  
运行时再决定对象的类型

2、动态绑定  
消息机制  
调用一个实例的方法，就是向该实例的指针发送消息，实例在收到消息后，从自身的实现中寻找响应这条消息的方法。

动态绑定所做的，就是在实例所属类确认后，将某些属性和相应的方法绑定到实例上。

3、动态加载  
根据需求加载所需要的资源，对于iOS开发来说基本就是根据不同的需求做适配。比如在Retina设备上加载@2x图，而在老的机器上加载原图。

