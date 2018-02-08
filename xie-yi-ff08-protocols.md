# 协议（Protocols）

Objective-C允许定义_协议（protocols）_，声明预期将用于特定情况的方法_。_

## 协议定义消息传递契约

协议用于声明独立于任何特定类的方法和属性。

定义协议的基本语法如下：

```
@protocol ProtocolName
// list of methods and properties
@end
```

协议可以包括实例方法和类方法的声明，以及属性。

### 运行时检测可选方法是否实现

如果一个协议中的方法被标记为可选的，你必须在尝试调用它之前先检查一个对象是否实现了这个方法。

例如：

```
NSString *thisSegmentTitle;
if ([self.dataSource respondsToSelector:@selector(titleForSegmentAtIndex:)]) {
    thisSegmentTitle = [self.dataSource titleForSegmentAtIndex:index];
}
```

如果你试图调用一个符合上面定义的协议的id的_respondsToSelector:_方法，你将得到一个对于它未知的实例方法的编译错误。一旦你使用一个协议修饰一个id，所有的静态检查就会回来；如果你试图调用任何指定协议里面没有定义的方法你将得到一个错误。避免编译错误的一个方法是设置自定义协议去采用NSObject协议。

### 协议继承自其他协议

可以指定一个协议继承自其它协议。

通过指出你自己的协议符合NSObject协议，你是在指出所有采用自定义协议的的对象也提供了每一个NSObject协议方法的实现。因为想必你会使用NSObject的子类，你不必担心自己提供这些NSObject方法的实现。

指定一个协议符合另个一个，你需要在尖括号里提供其他协议的名字，如下：

```
@protocol MyProtocol <NSObject>
...
@end
```

在这个例子里，任何采用MyProtocol协议的对象也有效的采用了声明在NSObject协议中声明的所有方法。

## Cocoa和Cocoa Touch定义大量的协议

一些协议被用于声明类之前的非层级相似性。一些协议涉及更多可能被多个不相关的类所采用的Cocoa和Cocoa Touch通信机制，而不是去被链接到特定类的要求。

例如，许多框架模型对象支持NSCoding协议，这意味着他们可以编码和解码他们的属性以作为原始数据归档或分发。NSCoding使把完整的对象图形写到磁盘变得简单，只要图中的每个对象都使用该协议即可。

一些Objective-C语言级别的特性也依赖协议实现。比如，为了使用快速枚举，一个集合必须采用NSFastEnumeration协议，如_快速枚举使枚举变得更容易_中所描述的。另外，一些对象可以被拷贝，比如当一个属性使用一个copy关键字。任何你尝试去拷贝的对象必须采用NSCopying协议，否则运行时你将得到一个异常。

## 协议被用于匿名

协议在一个对象的类型不清楚或者需要隐藏的情况下下也是有用的。

