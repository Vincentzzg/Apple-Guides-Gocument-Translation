# 语言

## Objective-C

Objective-C在ANSI C编程语言的基础上定义了一个小而强大的扩展集，用以实现复杂的面向对象编程。Objective-C是Cocoa编程的本地语言，它也是框架编写和编写很多的应用程序的语言。你也可以使用一些其他语言，比如Python和Ruby，使用Cocoa框架开发程序。尽管可以这样，还是需要对Objective-C有一个基础的了解，因为Apple的文档和代码示例都是用这个语言编写的。

因为Objective-C基于ANSI C的基础，因此你可以直接混编C代码和Objective-C。而且，你的代码可以调用定义在非Cocoa编程接口中的函数，比如/usr/include中的BSD库接口。你甚至可以将C++代码与Cocoa代码混合并将它们链接到相同的可执行文件中。

---

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

Objective-C是开发Cocoa和Cocoa Touch应用程序的本地主要语言。但是，Cocoa和Cocoa Touch的应用程序工程中也可以包含C++和ANSI C的代码。此外，你也可以使用桥接到Objective-C运行时的脚本语言，例如PyObjcC和RubyCocoa，来开发Cocoa应用程序。

### Objective-C是动态语言（Dynamic Language）

之前提到过，我们需要使用指针跟踪内存中的对象。由于Objective-C的动态特性，对象指针使用的具体类型并不重要，在你给相关对象发送消息的时候总会是正确的方法被调用。

id类型定义了一个通用的对象指针。

因为对象的类是在运行时确定的，所以在创建或使用实例时分配给变量什么类型没有区别。

### 对象比较

如果你要比较两个对象是否相同，请务必记住你正在使用指针。

标准C的相等运算符==用于测试两个变量的值之间的相等性，如下所示：

```
if (someInteger == 42) {
    // someInteger has the value 42
}

```

如果处理对象的话，相等运算符是在测试两个单独的指针是否指向同一个对象：

```
if (firstPerson == secondPerson) {
    // firstPerson is the same object as secondPerson
}

```

如果你需要测试两个对象是否代表相同的数据，你需要调用NSObject中的isEqual:方法：

```
if ([firstPerson isEqual:secondPerson]) {
    // firstPerson is identical to secondPerson
}

```

如果你需要比较是否一个对象代表的值大于或小于另一个对象，你不能使用标准C的比较运算符&gt;和&lt;。反而，基本的基础类型，比如NSNumber，NSString和NSDate提供饿了一个compare:方法：

```
if ([someDate compare:anotherDate] == NSOrderedAscending) {
    // someDate is earlier than anotherDate
}

```

### nil

在声明它们时初始化纯量变量总是一个好主意，否则它们的初始值将包含之前栈内容的垃圾信息：

```
BOOL success = NO;
int magicNumber = 42;

```

不过对于对象指针这不是必须的，因为如果你没有指定初始化值编译器会自动将变量设为nil：

```
XYZPerson *somePerson;
// somePerson is automatically set to nil

```

如果没有其他值可用的话，nil值是初始化对象指针最安全的方式。因为在Objective-C中向nil发送消息是完全可以接受的。如果你向nil发送一个消息，什么事都不会发生。

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

