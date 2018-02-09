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

### Objective-C定义了额外的原始类型

像NSInteger和NSUInteger这些类型，根据目标体系结构不同有不同的定义。在32位环境构建时（比如iOS），它们分别是32位有符号和无符号整数；在64环境构建时（如现代OS X运行时），它们分别是64位有符号和无符号整数。

如果你可能跨API边界（内部和到处的API）传递值，例如应用程序和框架之间的方法或函数调用中的参数或返回值，则最好使用这些特定于平台的类型。

对于局部变量，比如循环中的计数器，如果你知道该值在标准范围内，则可以使用基本C类型。

### C结构可以保持原始值

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

### 数字由NSNumber类的实例表示

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

## 大多数集合是对象

尽管可以使用C数组保存标量值的集合，甚至对象指针，大多数Objective-C代码中的集合是Cocoa和Cocoa Touch集合类其中一个类的实例，比如NSArray，NSSet和NSDictionary。

这些类被用于管理一组对象，意味着任何你想添加到一个集合的项必须是一个Objective-C雷的实例。如果你需要添加一个标量值，必须先创建一个合适的NSNumber或NSValue实例去表示它。

集合类使用强引用跟中它的内容，而不是以某种方式维护每个收集的对象的单独的拷贝。这意味着任何添加到集合中的对象将至少存活的像集合一样长。

为了跟踪它们的内容，每一个Cocoa和Cocoa Touch集合类使执行某些任务变得简单，例如枚举，访问特定的项目，或找出一个特定对象是否是集合的一部分。

基础的NSArray，NSSet和NSDictionary类是不可变的，意味着他们的内容在初始化是设置的。都各有一个可变子类允许你在需要的时候去添加或者移除对象。

### 数组是有序集合

数组被用作表示对象的有序集合。唯一的要求是每一个项目都是一个Objective-C对象，不要求每一个对象都是以同一个类的实例。

要维护数组中的顺序，每个元素都存储在从零开始的索引处，如下所示：

#### 创建数组

有很多不同的初始化和工厂方法可用，具体取决于对象的数量：

```
+ (id)arrayWithObject:(id)anObject;
+ (id)arrayWithObjects:(id)firstObject, ...;
- (id)initWithObjects:(id)firstObject, ...;
```

_arrayWithObjects:_和_initWithObjects:_方法都采用一个nil结尾的可变数量的参数，这意味着你必须包含nil作为最后一个值，如下所示：

```
NSArray *someArray = [NSArray arrayWithObjects:someObject, someString, someNumber, someValue, nil];
```

如果其中一个值为零，则可能会无意中中断项目列表，如下所示：

```
   id firstObject = @"someString";
    id secondObject = nil;
    id thirdObject = @"anotherString";
    NSArray *someArray = [NSArray arrayWithObjects:firstObject, secondObject, thirdObject, nil];
```

这种情况，_someArray_只会包含firstObject，因为nil _secondObject_将被解释为列表的最后一项。

#### 字面值语法

也可以使用Objective-C字面值创建数组，如下所示：

```
    NSArray *someArray = @[firstObject, secondObject, thirdObject];
```

#### 查询数组对象

一旦你创建了一个数组，你可以查询它的信息，比如对象个数，或者是否包含某个给定的项目：

```
    NSUInteger numberOfItems = [someArray count];

    if ([someArray containsObject:someString]) {
        ...
    }
```

你也可以查询数组中给定下标的项目。如果你试图请求一个非法的下标，你将得到一个越界的异常，所以首先你应该先检查项目数：

```
    if ([someArray count] > 0) {
        NSLog(@"First item is: %@", [someArray objectAtIndex:0]);
    }
```

##### 下标

还有个下标语法作为objectAtIndex:方法的替代，就像在标准C数组中获取一个值一样。上面的例子可以重写成这样：

```
    if ([someArray count] > 0) {
        NSLog(@"First item is: %@", someArray[0]);
    }
```

#### 排序数组对象

NSArray类也提供各种方法来对其收集的对象进行排序。因为NSArray是不可变的，每个这些方法都会返回一个新的数组，其中包含按排序顺序排列的项目。

例如，你可以通过对每一个字符串调用_compare:_方法返回的结果排序一个字符串数组，如下所示：

```
    NSArray *unsortedStrings = @[@"gammaString", @"alphaString", @"betaString"];
    NSArray *sortedStrings =
                 [unsortedStrings sortedArrayUsingSelector:@selector(compare:)];
```

#### 可变性

尽管NSArray类自己是不可变的，但这对任何收集的对象都没有影响。如果你添加一个可变字符串到数组，例如：

```
NSMutableString *mutableString = [NSMutableString stringWithString:@"Hello"];
NSArray *immutableArray = @[mutableString];
```

没有什么可以阻止你修改这个字符串：

```
    if ([immutableArray count] > 0) {
        id string = immutableArray[0];
        if ([string isKindOfClass:[NSMutableString class]]) {
            [string appendString:@" World!"];
        }
    }
```

如果你需要在一个数组创建之后向其中添加或从中移除对象，有需要使用NSMutableArray，其中添加了很多方法去添加、移除或替换一个或多个对象：

```
    NSMutableArray *mutableArray = [NSMutableArray array];
    [mutableArray addObject:@"gamma"];
    [mutableArray addObject:@"alpha"];
    [mutableArray addObject:@"beta"];

    [mutableArray replaceObjectAtIndex:0 withObject:@"epsilon"];
```

可以一步到位的排序一个可变数组，而不用创建第二个数组：

```
[mutableArray sortUsingSelector:@selector(caseInsensitiveCompare:)];
```

### 集合是无需的集合

一个NSSet类似于一个数组，但是维护一个无序的不同对象集合，如下所示：

由于集合不维护顺序，因此在测试成员资格时，它们提供了比数组更好的性能改进。

基础NSSet类也是不可变的，所以它的内容必须在创建时使用分配和初始化方法或者类工厂方法指定，如下所示：

```
    NSSet *simpleSet = [NSSet setWithObjects:@"Hello, World!", @42, aValue, anObject, nil];
```

集合仅存储一个单独对象的引用，即使你尝试和添加一个对象多次：

```
    NSNumber *number = @42;
    NSSet *numberSet =
      [NSSet setWithObjects:number, number, number, number, nil];
    // numberSet only contains one object
```

### 字典收集键值对

字典不是简单的维护一个有序的或无序的对象集合，而是根据给定的键存储对象，然后可以用它来找回。

用字符串对象作为字典键是最好的做法，如下：

> 注意：用其他对象作为键也是可以的，但是重要的是需要注意到每个键被拷贝供字典使用，所以必须支持NSCopying协议。

#### 创建字典

你可以使用分配和初始化创建字典，或者类工厂方法，如下所示：

```
    NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:
                   someObject, @"anObject",
             @"Hello, World!", @"helloString",
                          @42, @"magicNumber",
                    someValue, @"aValue",
                             nil];
```

##### 字面值语法

Objective-C也提供了字典创建的字面值语法，如下所示：

```
    NSDictionary *dictionary = @{
                  @"anObject" : someObject,
               @"helloString" : @"Hello, World!",
               @"magicNumber" : @42,
                    @"aValue" : someValue
    };
```

#### 查询字典

一旦你创建了一个字典，你可以查询一个键对应的存储对象，如下所示：

```
    NSNumber *storedNumber = [dictionary objectForKey:@"magicNumber"];
```

也有下标语法替代使用_objectForKey:_方法，像这样：

```
    NSNumber *storedNumber = dictionary[@"magicNumber"];
```

#### 可变性

如果在创建字典之后需要添加或移除对象，你需要使用NSMutableDictionary子类，如下所示：

```
    [dictionary setObject:@"another string" forKey:@"secondString"];
    [dictionary removeObjectForKey:@"anObject"];
```

### 使用NSNull表示nil

不能添加nil到这个章节描述的集合类中因为nil在Objective-C中意味着“没有对象”。如果你需要在集合中表示“没有对象”，你可以使用NSNull类：

```
    NSArray *array = @[ @"string", @42, [NSNull null] ];
```

NSNull是一个单例类，意味着null方法会一直返回同样的实例。这意味着你可以检测数组中的一个对象是否等于共享的NSNull实例：

```
    for (id object in array) {
        if (object == [NSNull null]) {
            NSLog(@"Found a null object");
        }
    }
```

## 使用集合存储对象图

NSArray和NSDictionary类可以很容易的将其内容直接存储到磁盘，如下所示：

```
    NSURL *fileURL = ...
    NSArray *array = @[@"first", @"second", @"third"];

    BOOL success = [array writeToURL:fileURL atomically:YES];
    if (!success) {
        // an error occured...
    }
```

如果每一个包含的对象都是属性列表类型之一（NSArray，NSDictionary，NSString，NSData，NSDate和NSNumber），可以从磁盘重新创建整个层次结构，如下所示：

```
    NSURL *fileURL = ...
    NSArray *array = [NSArray arrayWithContentsOfURL:fileURL];
    if (!array) {
        // an error occurred...
    }
```

## 使用最有效的集合枚举技术

Objective-C和Cocoa或Cocoa Touch提供多种方式枚举集合中的内容。尽管也可以使用传统C for循环遍历内容，如下所示：

```
    int count = [array count];
    for (int index = 0; index < count; index++) {
        id eachObject = [array objectAtIndex:index];
        ...
    }
```

最好的做法是使用这个部分描述的其他技术。

### 快速枚举使枚举一个集合变得简单

许多集合类符合_NSFastEnumeration_协议，包括NSArray，NSSet和NSDictionary。这意味着你可以使用快速枚举，一个Objective-C语言级别的特性。

枚举数组或集合内容的快速枚举语法像这样：

```
    for (<Type> <variable> in <collection>) {
        ...
    }
```

例如，有可能使用快速枚举打印数组中每个对象的描述日志，如下所示：

```
    for (id eachObject in array) {
        NSLog(@"Object: %@", eachObject);
    }
```

如果你快速遍历一个字典，遍历字典键，如下所示：

```
    for (NSString *eachKey in dictionary) {
        id object = dictionary[eachKey];
        NSLog(@"Object: %@ for key: %@", object, eachKey);
    }
```

快速遍历很像标准C for循环，所以你可以使用break关键字打断循环，或者continue继续前进到下一个元素。

即便集合是可变的，也不能在**快速遍历**时改变一个集合。如果你试图在循环中添加或移除一个集合对象，你将得到一个运行时异常（_Terminating app due to uncaught exception 'NSGenericException', reason: '\*\*\* Collection &lt;\_\_NSArrayM: 0x608000056c80&gt; was mutated while being enumerated.'\_）。使用传统C for循环遍历一个可变集合时修改集合内容，不会崩溃。

### 大部分集合也支持枚举器对象

也可以通过使用NSEnumerator对象遍历许多Cocoa和Cocoa Touch集合。

例如，你可以从NSArray请求一个_objectEnumerator_或一个_reverseObjectEnumerator。_可以对这些对象使用快速枚举，如下所示：

```
    for (id eachObject in [array reverseObjectEnumerator]) {
        ...
    }
```

这个例子中，循环将倒序枚举集合对象，所以最后一个对象将变成第一个，以此类推。

也可以通过重复调用枚举器的_nextObject_方法来枚举内容，如下所示：

```
    id eachObject;
    while ( (eachObject = [enumerator nextObject]) ) {
        NSLog(@"Current object is: %@", eachObject);
    }
```

在这个例子中，一个_while_循环被用于在每一次循环设置_eachObject_到下一个对象。当没有更多对象的时候，nextObject方法将返回nil，其逻辑值为false，所以循环停止。

### 一些集合支持基于Block的枚举

也可以使用块（block）枚举NSArray，NSSet和NSDictionary。

