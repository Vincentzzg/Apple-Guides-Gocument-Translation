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

一个类的主接口用于定义其他类可以与其进行的交互方式。换句话说，它是这个类的公共接口。

类扩展通常被用于通过提供类内部使用的额外的私有方法或属性来扩展公共接口。例如，在接口中将属性定义为只读，但是为了使内部方法可以直接修改其属性，在实现之前在类扩展中定义为可读可写。

例如：

```
@interface XYZPerson : NSObject
...
@property (readonly) NSString *uniqueIdentifier;
- (void)assignUniqueIdentifier;
@end
```

这意味着uniqueIdentifier不可能被外部的其他对象直接设置。如果一个人还没有，必须通过调用assignUniqueIdentifier方法请求分配一个标识符。

为了XYZPerson类能在内部修改修改这个属性，在实现文件顶部定义的类扩展中重新声明这个属性是有意义的。

```
@interface XYZPerson ()
@property (readwrite) NSString *uniqueIdentifier;
@end

@implementation XYZPerson
...
@end
```

> _readwrite_关键字是可选的，因为它是默认的。为了清晰起见，你可能会在重复声明的时候使用它。

这意味着编译器现在还是会合成一个setter方法，所以XYZPerson实现中的任何方法都能直接使用setter方法或者点语法设置属性。

通过在XYZPerson实现源码文件中声明类扩展，信息对XYZPerson保持私有。如果其他对象试图设置这个属性，编译器会报错。



