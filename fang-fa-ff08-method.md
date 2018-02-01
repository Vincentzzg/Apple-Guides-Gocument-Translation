# 方法

方法声明指明了一个对象可以接收的消息

在Objective-C中，对象之间通过发送消息进行交互，一个对象通过调用另一个对象的方法来将消息发送给这个对象。

Objective-C方法在概念上类似于C和其他编程语言中的标准函数。

一个C函数的声明如下：

```
void SomeFunction();
```

等价的Objective-C方法声明如下：

```
- (void)someMethod;
```

方法名前的 - 号说明这是一个`实例方法（Instance Method）`，可以被当前类的所有实例调用。区别于`类方法（Class Method）`，只能被类调用。

## 方法带参数

Objective-C方法声明包含参数作为方法名的一部分，如下：

```
- (void)someMethodWithValue:(SomeType)value;
```

两个参数：

```
- (void)someMethodWithFirstValue:(SomeType)value1 secondValue:(AnotherType)value2;
```



**基本的消息程序流程**

![](/assets/programflow1.png)



