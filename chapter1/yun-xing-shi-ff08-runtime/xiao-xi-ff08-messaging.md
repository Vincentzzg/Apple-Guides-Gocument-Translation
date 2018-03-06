# 消息（Messaging）

这个章节描述消息表达式如何被转换成objc\_msgSend函数调用，以及如何通过名称引用方法。然后解释如何利用objc\_msgSend，以及如果需要你如何绕过动态绑定。

## objc\_msgSend函数（The objc\_msgSend Function）

在Objective-C，消息直到运行时才被绑定到方法实现。编译器转换一个消息表达式，

```
[receiver message]
```

到一个消息函数的调用，objc\_msgSend。这个函数将接受者和消息中提到的方法实现的名称（即方法选择器）作为其两个主要参数：

```
objc_msgSend(receiver, selector)
```

消息中传递的任何参数也将传递给objc\_msgSend：

```
objc_msgSend(receiver, selector, arg1, arg2, ...)
```

消息传递功能完成动态绑定所需的一切：

* 它首先找到选择器引用的过程（方法实现）。由于同样的方法可以通过不同的类产生不同的实现，它发现的精确过程依赖于接受者的类。
* 然后它调用过程，将接收的对象（指向其数据的指针）以及为该方法指定的任何参数传递给过程。
* 最后，它传递过程的返回值作为它自己的返回值。

> 注意：编译器生成对消息传递函数的调用。你不应该在你写的代码中直接调用它。

## 

## 使用隐藏的参数（Using Hidden Arguments）

## 获取方法的地址（Getting a Method Address）



