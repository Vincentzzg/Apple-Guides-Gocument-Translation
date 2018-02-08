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



## Cocoa和Cocoa Touch定义大量的协议

一些协议被用于声明类之前的非层级相似性。一些协议涉及更多可能被多个不相关的类所采用的Cocoa和Cocoa Touch通信机制，而不是去被链接到特定类的要求。





