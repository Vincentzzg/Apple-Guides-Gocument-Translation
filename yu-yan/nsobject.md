# NSObject

根类不继承自任何其他类，而是统一给所有继承自它的对象定义接口和行为。继承链里面所有的对象最终都继承自根类。根类就是基类。

Objective-C所有类的根类都是NSObject，NSObject也是Foundation框架的一部分。Cocoa或Cocoa Touch应用程序的所有对象最终都继承自NSObject。这个类是其他类跟Objective-C运行时交互的主要接入点。它也定义了基础对象接口并实现了基础的对象行为，包括自省，内存管理，方法调用。Cocoa和CocoaTouch对象从根类派生出大部分对象的能力。

![](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Art/root_class_2x.png)

根类NSObject类采用了一个协议，名字也是NSObject，它有助于其编程接口。该协议指定了任何根类所需的基础编程接口。

