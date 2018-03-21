## 类（**Classes**）、对象（**Objects**）

所有事务都是对象，都有isa指针。

对象在内存中的排布可以看成一个结构体，该结构体的大小并不能动态更改。所以无法在运行时给对象增加成员变量。  
但是对象的方法都保存在类的可变区域中，方法定义的列表是一个指针的指针，可以通过修改指针指向的指针的值，实现动态的给一个类增加成员方法（**Category可以为对象增加成员方法，但是不能增加成员变量**）。

#### 对象

对象用相关行为打包数据。

一个应用程序（APP）被构建为一个由互相连接的对象之间互相通信以解决特定的问题的大型生态系统。例如展示可视界面，响应用户输入或存储信息。

#### 对象、**类**

在面向对象的编程术语中，对象是类的实例。

类是对象的蓝图。

类描述了任何特定类型的对象所共有的行为和属性。

一个类的接口（Interface）定义了类的预期交互，声明接口的语法：

```
@interface SimpleClass : NSObject

@end
```

#### 

#### 字符串字面值

@“Hello, world!” -- Objective-C字符串字面值（string literal）

字符串是Objective-C中几个类类型之一，支持简单的字面值语法创建对象。

@“Hello, world!”在概念上相当于一个值是_Hello, world!_的字符串对象。

#### 

#### 使用指针来追踪对象（Use Pointers To Keep Track Of Objects）

一个对象通常要比为了追踪它而创建的原始变量保持更长的时间，所以一个对象的内存被动态的分配和解除分配。

局部变量（local variable）被分配在栈内存上，对象（objects）被分配在堆内存上。

这就需要使用C指针（保持内存地址）来跟踪他们在内存里的位置，如下：

```
- (void)myMethod {
    NSString *myString = // get a string from somewhere...
    [...]
}
```

尽管指针变量_myString_的范围（\*表示它是一个指针）仅限于myMethod方法的范围，但它执行内存中的实际字符串对象的寿命可能会超出该范围。它可能已经存在，或者你可能需要在其他方法调用中传递这个对象。

### 对象是动态创建的（Objects Are Created Dynamically）

Objective-C对象的内存是动态分配的。创建对象的第一步就是确保足够的内存，不仅分配给对象类定义的属性，还分配给继承链中的每个父类定义的属性。

NSObject根类提供了一个类方法，alloc，帮组我们处理了这个过程：

```
+ (id)alloc;
```

allo方法还有个重要的作用，通过将分配给对象属性的内存设置为零来清楚内存。这样可以避免以前存储的内存中包含垃圾的常见问题，但不足以完全初始化对象。

还需要NSObject中的另一个方法init一起使用：

```
- (id)init;
```

init方法用来确保属性在创建的时候有合适的初始值。

### 初始化方法可以带参数

比如NSNumber类定义了多个初始化器，包括：

```
- (id)initWithBool:(BOOL)value;
- (id)initWithFloat:(float)value;
- (id)initWithInt:(int)value;
- (id)initWithLong:(long)value;
```

### 类工厂方法是分配和初始化的替代

NSNumber类定义了几个对应的工厂方法，包括：

```
+ (NSNumber *)numberWithBool:(BOOL)value;
+ (NSNumber *)numberWithFloat:(float)value;
+ (NSNumber *)numberWithInt:(int)value;
+ (NSNumber *)numberWithLong:(long)value;
```





