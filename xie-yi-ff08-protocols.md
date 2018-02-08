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





## Cocoa和Cocoa Touch定义大量的协议

一些协议被用于声明类之前的非层级相似性。一些协议涉及更多可能被多个不相关的类所采用的Cocoa和Cocoa Touch通信机制，而不是去被链接到特定类的要求。

例如，许多框架模型对象支持NSCoding协议，这意味着他们可以编码和解码他们的属性以作为原始数据归档或分发。NSCoding使把完整的对象图形写到磁盘变得简单，只要图中的每个对象都使用该协议即可。

一些Objective-C语言级别的特性也依赖协议实现。比如，为了使用快速枚举，一个集合必须采用NSFastEnumeration协议，如_快速枚举使枚举变得更容易_中所描述的。另外，一些对象可以被拷贝，比如当一个属性使用一个copy关键字。任何你尝试去拷贝的对象必须采用NSCopying协议，否则运行时你将得到一个异常。

## 协议被用于匿名

协议在一个对象的类型不清楚或者需要隐藏的情况下下也是有用的。







