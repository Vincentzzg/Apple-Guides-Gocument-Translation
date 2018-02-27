# 封装数据（Encapsulating Data）

## 属性封装对象的值

### 为暴露数据声明公共属性

### 使用访问器方法来获取或设置属性

### 点语法是访问器方法调用的简洁替代方法

### 大多数属性都由实例变量支持

### 直接从初始化方法访问实例变量

### 你可以实现自定义的访问器方法

### 属性默认是原子的（Properties Are Atomic by Default）

默认情况下，Objective-C属性是原子的：

```
@interface XYZObject : NSObject
@property NSObject *implicitAtomicObject;          // atomic by default
@property (atomic) NSObject *explicitAtomicObject; // explicitly marked atomic
@end
```

这意味着合成的访问器确保一个值总是通过getter方法完全检索或通过setter方法完全设置，即使访问器方法是从不同的线程同时调用的。

由于原子访问器方法的内部实现和同步是私有的，因此不可能将一个合成的访问器和一个你实现的访问器方法组合在一起。例如，如果你试图给一个atomic、readwrite的属性提供自定义的setter方法，但是让编译器去合成getter方法，编译器将会报警告。

你可以使用noatomic属性关键字指明合成访问器方法只是简单的设置或直接返回一个值，不保证同样的值同时被不同的线程访问时会发生什么。处于这个原因，访问一个非原子的（noatomic）属性比一个原子的（atomic）属性更快，而且可以组合合成setter方法和你自己的getter实现：

```
@interface XYZObject : NSObject
@property (nonatomic) NSObject *nonatomicObject;
@end
```

> 注意：属性的原子性与对象线程安全不是同义的。
>
> [_Concurrency Programming Guide_](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091)中更详细地介绍了线程安全。

## 通过所有权和责任来管理对象图

避免强引用循环（Avoid Strong Reference Cycles）

使用strong和weak声明来管理所有权（Use Strong and Weak Declarations to Manage Ownership）

对某些类使用不安全的未保留引用（Use Unsafe Unretained References for Some Classes）

拷贝属性保持它们自己的拷贝（Copy Properties Maintain Their Own Copies）

