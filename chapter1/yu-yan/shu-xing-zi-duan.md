# 属性关键字

## **@property**

对象通过属性来封装数据（encapsulates data）

属性声明包含在在一个类的接口中，like this:

```
@interface XYZPerson : NSObject
@property NSString *firstName;
@property NSString *lastName;
@end
```

语法

```
@property (<#attributes#>) <#type#> <#name#>;
```

@property声明的属性，编译器会自动合成getter、setter方法

例子：

```
@property(copy) NSString *title;
```

上面的语法相当于是声明了下面两个存取器方法：

```
- (NSString *)title;
- (void)setTitle:(NSString *)newTitle;
```



# 属性关键字

@property的常用属性关键字有nonatomic、atomic、readonly、writeonly、readwrite、assign、retain、copy、strong、weak、unsafe\_unretained、nonnull、nullable、null\_resettable，看着挺多的，但是经常用的也就那几个。

分三类：  
1.是否加线程锁

* atomic:默认关键字，也就是说如果什么都不写，默认就是这个。表示该属性是线程同步的。一般用不到，会影响性能。
* nonatomic:非线程同步，基本都是用这个

2.是否成成setter方法

* readwrite：默认关键字，表示可读可写，无需写出
* readonly：编译器会合成getter方法，不合成setter方法，当你希望属性不能通过setter方法被修改，但是可以访问时使用。 
* writeonly: 只能写\(setter\)，不能读\(getter\)。一般用不到。

3.setter方法中如何传递所有权等内存管理策略，ARC环境默认为strong

* assign:默认关键字。非对象类型一般使用此关键字。
* retain:对象的引用计数+1。ARC下已经不再使用此关键字，用strong代替。
* copy:拷贝一个新的对象，新对象的引用计数+1，原对象不变。
* strong:能够维持对象的生命。





参考文章：

苹果官方文档：

[https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/DeclaredProperty.html](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/DeclaredProperty.html)[https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html\#//apple\_ref/doc/uid/TP40011210-CH5-SW2](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW2)



