# 类扩展（Class Extensions）

## 类扩展扩展了内部实现

```
类扩展跟分类有些相似，但它只能被添加到编译时有源码的类中（类跟类扩展同时编译）。类扩展里面声明的方法需要在原始类的@implementation部分实现，因此，不能给框架类添加扩展，比如像NSString这样的Cocoa或Cocoa Touch类。
```

声明类扩展的语法跟分类的语法很像，像这样：

```
@interface ClassName ()
 
@end
```

因为括号里没有名字，类扩展通常被称为匿名分类。

与常规类别不同，类扩展可以给类添加它自己的属性和实例变量。如果在类扩展里声明属性，可以这样：

```
@interface XYZPerson ()
@property NSObject *extraProperty;
@end
```

编译器会在主类实现中自动合成相关的存取方法以及一个实例变量。

如果你在类扩展里添加了任何方法，必须在类的主要实现中实现这些方法。

也可以使用类扩展添加自定义的实例变量。

这些在类扩展的大括号中声明：

```
@interface XYZPerson () {
    id _someCustomInstanceVariable;
}
...
@end
```

## 使用类扩展隐藏私有信息





