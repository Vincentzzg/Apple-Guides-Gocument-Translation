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

### 格式化字符串被用于从其他对象或值构建字符串

如果你需要构建包含变量值的字符串，你需要使用格式化字符串。这使你可以使用格式说明符来只是如何插入值：

```
    int magicNumber = ...
    NSString *magicString = [NSString stringWithFormat:@"The magic number is %i", magicNumber];
```

## 数字由NSNumber类的实例表示

NSNumber类被用作表示任何基础C标量类型，包括char，double，float，int，long，short，和每个的无符号变体，以及Objective-C布尔值BOOL。

像NSString一样，你有很多种选择去创建NSNumber实例，包括分配和初始化或类工厂方法：

```
    NSNumber *magicNumber = [[NSNumber alloc] initWithInt:42];
    NSNumber *unsignedNumber = [[NSNumber alloc] initWithUnsignedInt:42u];
    NSNumber *longNumber = [[NSNumber alloc] initWithLong:42l];

    NSNumber *boolNumber = [[NSNumber alloc] initWithBOOL:YES];

    NSNumber *simpleFloat = [NSNumber numberWithFloat:3.14f];
    NSNumber *betterDouble = [NSNumber numberWithDouble:3.1415926535];

    NSNumber *someChar = [NSNumber numberWithChar:'T'];
```

也可以使用Objective-C字面值语法创建NSNumber实例：

```
    NSNumber *magicNumber = @42;
    NSNumber *unsignedNumber = @42u;
    NSNumber *longNumber = @42l;

    NSNumber *boolNumber = @YES;

    NSNumber *simpleFloat = @3.14f;
    NSNumber *betterDouble = @3.1415926535;

    NSNumber *someChar = @'T';
```

这些例子等价于使用NSNumber类工厂方法。

一旦你创建了一个NSNumber实例，就可以使用其中一个访问器方法来请求标量值：

```
    int scalarMagic = [magicNumber intValue];
    unsigned int scalarUnsigned = [unsignedNumber unsignedIntValue];
    long scalarLong = [longNumber longValue];

    BOOL scalarBool = [boolNumber boolValue];

    float scalarSimpleFloat = [simpleFloat floatValue];
    double scalarBetterDouble = [betterDouble doubleValue];

    char scalarChar = [someChar charValue];
```

NSNumber类也提供了额外的与Objective-C基本类型相关的方法。例如，如果你需要创建一个对象表示标量NSInteger和NSUInteger类型，确保使用正确的方法：

```
    NSInteger anInteger = 64;
    NSUInteger anUnsignedInteger = 100;
 
    NSNumber *firstInteger = [[NSNumber alloc] initWithInteger:anInteger];
    NSNumber *secondInteger = [NSNumber numberWithUnsignedInteger:anUnsignedInteger];
 
    NSInteger integerCheck = [firstInteger integerValue];
    NSUInteger unsignedCheck = [secondInteger unsignedIntegerValue];
```

所有的NSNumber实例都是不可变的，而且没有可变子类；如果你需要一个不同的数字，只需要使用另一个NSNumber实例。

> 注意：NSNumber实际上是一个类集群。这意味着当你在运行时创建一个实例，你会得到一个合适的具体子类持有提供的值。只要把创建的对象作为NSNumber的一个实例。

### 使用NSValue类的实例表示其他值

NSNumber类自己就是基础NSValue类的子类，它提供一个围绕单个值和数据项的对象封装。另外对于基础C标量类型，NSValue也可以被用作表示指针和结构体。

NSValue类提供多个工厂方法来创建具有给定标准结构的值，使创建实例表示一个（例如）NSRange变得简单：

```
    NSString *mainString = @"This is a long string";
    NSRange substringRange = [mainString rangeOfString:@"long"];
    NSValue *rangeValue = [NSValue valueWithRange:substringRange];
```

创建NSValue对象表示自定义的结构也是可以的。如果你有一个特殊需求需要使用C结构（而不是Objective-C对象）来存储信息，如下：

```
typedef struct {
    int i;
    float f;
} MyIntegerFloatStruct;
```

你可以创建一个NSValue实例通过提供一个指向结构的指针以及一个Objective-C编码类型。_@encode\(\)_编译器指令用于创建正确的Objective-C类型，如下所示：

```
    struct MyIntegerFloatStruct aStruct;
    aStruct.i = 42;
    aStruct.f = 3.14;
 
    NSValue *structValue = [NSValue value:&aStruct
                             withObjCType:@encode(MyIntegerFloatStruct)];
```

标准C参考运算符（&）用于为止参数提供aStruct的地址。



