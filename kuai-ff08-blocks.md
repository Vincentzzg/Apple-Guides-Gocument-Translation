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



### 

### Blocks可以捕捉括号范围内的值

### 可以把Blocks作为参数传递给方法或函数

### 使用类型定义来简化Block语法

### 对象可以使用属性跟踪Blocks

### 使用self时避免强引用循环

## Blocks可以简化枚举

## Blocks可以简化并发任务

### 操作队列使用Block操作

### 使用GCD调度队列时调度Blocks



