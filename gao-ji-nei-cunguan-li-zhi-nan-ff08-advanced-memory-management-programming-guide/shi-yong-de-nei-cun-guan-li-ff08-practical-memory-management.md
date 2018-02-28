# 实用的内存管理（Practical Memory Management）

尽管[Memory Management Policy](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmRules.html#//apple_ref/doc/uid/20000994-BAJHFBGH)中描述的基本概念是直接了当的，仍然有一些实用的步骤你可以采取以使内存管理更简单，并且帮助你确保你的程序在最小化资源请求的同时保持可靠和强大。

## 使用访问器方法使内存管理更容易

考虑一个需要设置数量的计数对象。

```
@interface Counter : NSObject
@property (nonatomic, retain) NSNumber *count;
@end
```

property声明了两个访问器方法。通常，你应该让编译器合成这些方法；然而，了解它们如何实施是很有益的。

“get”访问器中，你必须返回合成的实例变量，所以不需要retain或release：

```
- (NSNumber *)count {
    return _count;
}
```

“set”方法中，如果其他人都按照相同的规则玩牌，则必须假定新的点数可以随时处置，因此你必须通过发送一个保留（retain）消息获取该对象的所有权--以确保它不会。你也必须通过给它发送一个释放（release）消息放弃对老的数量对象的所有权。（在Objective-C中给nil发送消息是被允许的，所以如果\_count还没有被设置实现依然可以工作。）如果两者是同一个对象，则必须在\[newCount retain\]后发送，你不想无意中导致它被释放。





### 使用访问器方法设置属性值

### 在初始化方法和dealloc方法中不要使用访问器方法

## 使用弱引用避免循环保持

## 避免导致你正在使用的对象解除分配

## 不要使用dealloc来管理稀缺资源

## 集合拥有它包含的对象

## 使用保留计数实现所有权策略



