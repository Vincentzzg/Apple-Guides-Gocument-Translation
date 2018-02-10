# 块（Blocks）

一个Objective-C类定义一个将数据和相关行为组合在一起的对象。有些时候，只需要表示一个单独的任务或行为单元，而不是一个方法的集合。

块是添加到C，Objective-C和C++的一个语言级别的特性，它允许创建可以像值一样在方法或函数间传递的不同的代码段。块（Blocks）是Objective-C对象，这意味着他们可以被添加到像NSArray或NSDictionary这样的集合中。他们也有能力捕捉封闭范围的值，这使它们与其他编程语言中的闭包（closures）或lambdas（lambdas）类似。

## 块（Block）语法

定义一个块字面值的语法使用插入符号（^），如下所示：

```
    ^{
         NSLog(@"This is a block");
    }
```

这个例子中，这个块没有返回值也没有参数。

像可以使用一个函数指针指向一个C函数一样，你可以声明一个变量跟踪一个块，如下所示：

```
void (^simpleBlock)(void);//定义一个变量跟踪一个块
```

这个例子声明了一个名为_simpleBlock_的变量去指向一个没有参数也没有返回值的块，这意味着这个变量可以被赋值上面展示的块字面值，如下所示：

```
    simpleBlock = ^{
        NSLog(@"This is a block");
    };
```

也可以合并变量声明和赋值：

```
    void (^simpleBlock)(void) = ^{
        NSLog(@"This is a block");
    };
```

一旦你声明和赋值了一个块变量，你可以使用它调用块：

```
simpleBlock();
```

> 如果你尝试使用一个没有赋值的变量调用一个块（一个nil块变量），你的APP会崩溃。

### Blocks有参数和返回值

块也可以像方法和函数一样有参数和返回值。

例如，考虑一个变量指向一个块返回两个值乘积的块：

```
    double (^multiplyTwoValues)(double, double);
```

相应的块字面值可能是这个样子：

```
    ^ (double firstValue, double secondValue) {
        return firstValue * secondValue;
    }
```

firstValue和secondValue用于引用块调用时提供的值，就像任何函数定义一样。这个例子中，返回类型是从块内的return语句推断出来的。

如果你喜欢，你可以通过在插入符和参数之间显式指定返回类型：

```
    ^ double (double firstValue, double secondValue) {
        return firstValue * secondValue;
    }
```

一旦你声明定义了块，你就可以像调用函数一样调用它：

```
    double (^multiplyTwoValues)(double, double) =
                              ^(double firstValue, double secondValue) {
                                  return firstValue * secondValue;
                              };

    double result = multiplyTwoValues(2,4);

    NSLog(@"The result is %f", result);
```

### Blocks可以捕捉括号范围内的值

由于包含了执行代码，一个块也可有能力从封闭的范围中捕获状态。

例如，如果在一个方法内声明一个块字面值，可以捕获方法范围内任何可以获取的值，如下所示：

```
- (void)testMethod {
    int anInteger = 42;

    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };

    testBlock();
}
```

在这个例子中，_anInteger_被声明在块外部，但是值在块定义的时候被捕获。

只有值被捕获，除非你另有指定。这意味着如果你在块定义和调用之间修改了变量的外部值，如下所示：

```
    int anInteger = 42;

    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };

    anInteger = 84;

    testBlock();
```

块捕获的值不受影响。这意味着日志输出依然是：

```
Integer is: 42
```

这也意味着块不能修改原始变量的值，甚至是捕获的值（它被捕获为一个常量变量）。强行修改会报错：_Variable is not assignable \(missing \_\_block type specifier\)\_。

#### 使用\_\_block变量共享存储

如果你需要在块内部修改捕获的变量的值，你可以在原始变量声明中使用_\_\_block\_存储类型修改器。这意味着存储中的变量在原始变量的词法范围和任何该范围内声明的块之间共享。

例如，你可以重写上面的例子如下：

```
    __block int anInteger = 42;

    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };

    anInteger = 84;

    testBlock();
```

因为_anInteger_被声明为_\_\_block\_变量，它的存储在块声明中共享。这意味着日志输出现在显示为：

```
Integer is: 84
```

这也意味着块可以修改原始值，如下所示：

```
    __block int anInteger = 42;

    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
        anInteger = 100;
    };

    testBlock();
    NSLog(@"Value of original variable is now: %i", anInteger);
```

这次，输出是：

```
Integer is: 42
Value of original variable is now: 100
```

### 可以把Blocks作为参数传递给方法或函数

在实践中，把块传递给函数或方法在其他地方调用很普遍。例如，你可能会使用Grand Central Dispatch在后台调用一个块，或者定义一个块表示一个需要重复调用的任务，比如枚举一个集合。

块也用于回调，定义代码在任务完成时执行。例如，你的APP可能需要通过创建一个完成复杂任务的对象响应用户的行为，比如从Web服务器请求信息。因为任务可能需要很长时间，在任务执行时你需要展示某种进度指示器，然后任务完成时隐藏指示器。

这也可以使用委托来实现：你需要创建一个合适的委托协议，实现必要的方法，设置对象作为任务的委托，然后等待任务完成时调用对象的委托方法。

然而，块使这个更简单，因为你可以在发起任务时定义回调行为，如下所示：

```
- (IBAction)fetchRemoteInformation:(id)sender {
    [self showProgressIndicator];

    XYZWebTask *task = ...

    [task beginTaskWithCallbackBlock:^{
        [self hideProgressIndicator];
    }];
}
```

```
代码可读性方面，该块使得在一个地方很容易看到任务完成之前和之后会发生什么，避免了跟踪代理方法去查看将要发生什么的必要性。
```

这个例子中展示的_beginTaskWithCallbackBlock_:方法看起来将会是这个样子：

```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock;
```

\(void \(^\)\(void\)\)说明参数是一个没有任何参数也没有任何返回值的块。该方法的实现可以以通常的方式调用该块：

```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock {
    ...
    callbackBlock();
}
```

带有一个或多个参数的块的方法参数的指定方式与块变量相同：

```
- (void)doSomethingWithBlock:(void (^)(double, double))block {
    ...
    block(21.0, 2.0);
}
```

#### 块应该始终是方法的最后一个参数

最好的做法是一个方法中只使用一个块参数。如果方法也需要其他非块的参数，块应该在最后：

```
- (void)beginTaskWithName:(NSString *)name completion:(void(^)(void))callback;
```

这使得在内联指定块时方法调用更易读，如下所示：

```
    [self beginTaskWithName:@"MyTask" completion:^{
        NSLog(@"The task is complete");
    }];
```

### 使用类型定义来简化Block语法

如果你需要定义具有相同签名的多个块，咋可能需要为改签名定义自己的类型。

例如，你可以为一个没有参数或返回值的简单的块定义一个类型，如下所示：

```
typedef void (^XYZSimpleBlock)(void);
```

你可以在块作为方法参数或创建块变量时使用你的自定义类型：

```
    XYZSimpleBlock anotherBlock = ^{
        ...
    };
```

```
- (void)beginFetchWithCallbackBlock:(XYZSimpleBlock)callbackBlock {
    ...
    callbackBlock();
}
```

处理块返回或将其他块作为参数时，自定义类型定义尤其有用。考虑下面的例子：

```
void (^(^complexBlock)(void (^)(void)))(void) = ^ (void (^aBlock)(void)) {
    ...
    return ^{
        ...
    };
};
```

complexBlock变量指向一个使用其他块作为参数（aBlock）且返回另外一个块的块。

使用一个类型定义重写这段代码使代码更可读：

```
XYZSimpleBlock (^betterBlock)(XYZSimpleBlock) = ^ (XYZSimpleBlock aBlock) {
    ...
    return ^{
        ...
    };
};
```

### 对象可以使用属性跟踪Blocks

定义一个跟踪块的属性语法跟块变量相似：

```
@interface XYZObject : NSObject
@property (copy) void (^blockProperty)(void);
@end
```

> 注意：你应该指定copy作为属性关键字，因为块需要被拷贝以跟踪它在原始范围外捕获的状态。

一个块属性被设置或调用就像任何其他块变量一样：

```
    self.blockProperty = ^{
        ...
    };
    self.blockProperty();
```

也可以使用类型定义声明块属性，如下所示：

```
typedef void (^XYZSimpleBlock)(void);
 
@interface XYZObject : NSObject
@property (copy) XYZSimpleBlock blockProperty;
@end
```

### 使用self时避免强引用循环

## Blocks可以简化枚举

## Blocks可以简化并发任务

### 操作队列使用Block操作

### 使用GCD调度队列时调度Blocks



