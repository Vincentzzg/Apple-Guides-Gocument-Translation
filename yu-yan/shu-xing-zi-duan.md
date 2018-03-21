# 属性关键字

## **@property**

对象通过属性来封装数据（encapsulatesdata），属性控制着对象的数据访问

属性声明包含在在一个类的接口中，likethis:

```
@interface XYZPerson : NSObject
@property NSString *firstName;
@property NSString *lastName;
@end
```

语法

```
@property (
<
#attributes#
>
) 
<
#type#
>
<
#name#
>
;
```

@property声明的属性，编译器会自动合成存取器（getter、setter）方法

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

属性关键字决定数据的可访问性和存储注意事项

## 使用strong和weak关键字管理所有权

## 

### **Copy**

Copy关键字修饰的属性，会保留设置给它的对象的一份拷贝。

任何设置给Copy属性的对象必须遵守NSCopying协议。

Copy关键字也是强引用，因为它必须保持它自己创建的新对象。

#### **NSString属性如果不使用Copy，会有什么问题？**

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

尽管当时赋值给badgeView.firstName的时候nameString的值是“John”，但现在它的值是“”

badgeView只应该只维护设置给firstName和lastName属性的任何字符串的一份拷贝，以便设置属性的时候捕获当时的字符串值，且不会随设置字符串的值改变而改变。添加一个Copy属性关键字就能解决：

```
@interface XYZBadgeView : NSView
@property (copy) NSString *firstName;
@property (copy) NSString *lastName;
@end
```

如果需要直接给一个Copy属性的实例变量赋值，例如在初始化方法中，不要忘记设置原始对象的拷贝：

```
- (id)initWithSomeOriginalString:(NSString *)aString {
    self = [super init];
    if (self) {
        _instanceVariableForCopyProperty = [aString copy];
    }

    return self;
}
```

#### 关键字

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

[Programming with Objective-C](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011210-CH1-SW1)

[DeclaredProperty](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/DeclaredProperty.html)

[Encapsulating Data](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW2)

