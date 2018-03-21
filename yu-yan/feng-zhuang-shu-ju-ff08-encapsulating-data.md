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

### 避免强引用循环（Avoid Strong Reference Cycles）

### 使用strong和weak声明来管理所有权（Use Strong and Weak Declarations to Manage Ownership）

### 对某些类使用不安全的未保留引用（Use Unsafe Unretained References for Some Classes）

### copy属性保持它们自己的拷贝（Copy Properties Maintain Their Own Copies）

在某些情况下，对象可能希望对设置给他的属性的任何对象保留一份自己的拷贝。

例如下面的XYZBadgeView类的接口部分：

```
@interface XYZBadgeView : NSView
@property NSString *firstName;
@property NSString *lastName;
@end

```

声明了两个NSString属性，它们都保持对其对象的隐式强引用。

如果另一个对象创建一个NSString字符串来设置给badgeView的firstName属性，如下：

```
NSMutableString *nameString = [NSMutableString stringWithString:@"John"];
self.badgeView.firstName = nameString;

```

这样的赋值是可行的，因为NSMutableString是NSString的一个子类。但是此时firstName已经指向了一个NSMutableString。

此时如果发生了下面的改变：

```
[nameString appendString:@"ny"];

```

尽管当时赋值给badgeView.firstName的时候nameString的值是“John”，但现在它的值是“Johnny”，因为可变字符串被修改了。

badgeView只应该只维护设置给firstName和lastName属性的任何字符串的一份拷贝，以便设置属性的时候捕获当时的字符串值，且不会随设置字符串的值改变而改变。添加一个Copy属性关键字就能解决：

```
@interface XYZBadgeView : NSView
@property (copy) NSString *firstName;
@property (copy) NSString *lastName;
@end
```

视图现在维持了两个字符串的自己的拷贝。即使被设置了一个可变字符串并随后改变，徽章视图（the badge view）捕获了它在设置时的值。例如：

```
    NSMutableString *nameString = [NSMutableString stringWithString:@"John"];
    self.badgeView.firstName = nameString;
    [nameString appendString:@"ny"];
```

这次，徽章视图持有的firstName将是原始“John”字符串的一份不受影响的拷贝。



copy属性关键字意味着属性将使用强引用，因为它必须持有它创建的新对象。

> 注意：任何想设置给copy属性的对象必须支持NSCopying，这意味着它应该符合NSCopying协议。
>
> 关于NSCopying的更多信息，请参阅[_Advanced Memory Management Programming Guide_](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)中的[NSCopying](https://developer.apple.com/documentation/foundation/nscopying)。

如果你需要直接设置一个copy属性的实例变量，例如在初始化方法中，不要忘记设置原始对象的拷贝：

```
- (id)initWithSomeOriginalString:(NSString *)aString {
    self = [super init];
    if (self) {
        _instanceVariableForCopyProperty = [aString copy];
    }
    return self;
}
```



