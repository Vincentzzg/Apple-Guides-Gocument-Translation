# 值和集合（Values and Collections）

## Objective-C中提供了基本C的原始类型

Objective-C中提供了每一个标准C标量变量类型：

```
int someInteger = 42;
float someFloatingPointNumber = 3.1415;
double someDoublePrecisionFloatingPointNumber = 6.02214199e23;
```

以及标准C的运算符：

```
int someInteger = 42;
someInteger++;            // someInteger == 43
 
int anotherInteger = 64;
anotherInteger--;         // anotherInteger == 63
 
anotherInteger *= 2;      // anotherInteger == 126
```

如果你给一个属性使用一个标量类型，如下：

```
@interface XYZCalculator : NSObject
@property double currentValue;
@end
```

当使用点语法获取属性值时使用C运算符也是可以的，如下：

```
@implementation XYZCalculator
- (void)increment {
    self.currentValue++;
}
- (void)decrement {
    self.currentValue--;
}
- (void)multiplyBy:(double)factor {
    self.currentValue *= factor;
}
@end
```



## Objective-C定义了额外的原始类型

像NSInteger和NSUInteger这些类型，根据目标体系结构不同有不同的定义。在32位环境构建时（比如iOS），它们分别是32位有符号和无符号整数；在64环境构建时（如现代OS X运行时），它们分别是64位有符号和无符号整数。

如果你可能跨API边界（内部和到处的API）传递值，例如应用程序和框架之间的方法或函数调用中的参数或返回值，则最好使用这些特定于平台的类型。

对于局部变量，比如循环中的计数器，如果你知道该值在标准范围内，则可以使用基本C类型。

## C结构可以保持原始值

## 

## 对象可以表示原始值

### 字符串由NSString类的实例来表示

有很多种方式创建NSString对象，包括标准分配和初始化，类工程方法或字面值语法：

```
    NSString *firstString = [[NSString alloc] initWithCString:"Hello World!"
                                                     encoding:NSUTF8StringEncoding];
    NSString *secondString = [NSString stringWithCString:"Hello World!"
                                                encoding:NSUTF8StringEncoding];
    NSString *thirdString = @"Hello World!";
```

基础NSString类是不可变的，意味着它的内容在初始化时设置且后面不能更改。如果你需要表示一个不同的字符串，你必须创建一个新的字符串对象，如下：

```
    NSString *name = @"John";
    name = [name stringByAppendingString:@"ny"];    // returns a new string object
```

NSMutableString类是NSString的可变子类，允许在运行时通过appendString:或appendFormat:方法修改字符内容，如下：

```
    NSMutableString *name = [NSMutableString stringWithString:@"John"];
    [name appendString:@"ny"];   // same object, but now represents "Johnny"
```





