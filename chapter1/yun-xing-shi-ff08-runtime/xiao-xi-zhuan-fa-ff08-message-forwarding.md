# 消息转发（Message Forwarding）

发送一个消息给不处理它的对象是一个错误。然而，在宣布这个错误前，运行时系统给接收对象第二次处理消息的机会。

## 转发（Forwarding）

如果发送一个消息给不处理它的对象，在宣布错误之前运行时给对象发送一个forwardInvocation:消息以及一个NSInvocation对象作为它的唯一参数-NSInvocation对象封装了原始消息以及它传递的参数。

你可以实现forwardInvocation:方法给这个消息一个默认的响应，或者以其他方式避免错误。顾名思义，forwardInvocation:通常被用作转发消息到其他对象。

了解转发的范围和意图，想象下面的场景：首先，假设你设计了一个可以响应名为negotiate的消息的对象，并且你希望它的响应包括另外一类对象的响应。你可以通过在你实现的negotiate方法体中发送negotiate消息给其他对象，轻松的实现这些。

更进一步，假设你希望你的对象对negotiate方法的响应完全是另外一个类中实现的响应。实现这个的一种方式是使你的类从其他类继承该方法。然而，不可能按照这种方式安排事情。为什么你的类和实现negotiate方法的类在继承层次结构的不同分支中可能有很好的理由。

即使你的类不能继承negotiate方法，你依然可以通过实现一个只将消息传递给其他类的实例的方法来“借”它：

```
- (id)negotiate
{
    if ( [someOtherObject respondsTo:@selector(negotiate)] )
        return [someOtherObject negotiate];
    return self;
}
```

这样做事的方式可能会变得麻烦一些，尤其如果你希望你的对象传递很多消息给其他对象时。你必须实现一个方法覆盖每一个你想从其他类借的方法。此外，在你编写代码的时候，处理你不知道的情况是不可能的，你可能想要转发的全部消息。该集合可能取决于运行时的事件，并且可能随着未来实现新的方法和类而改变。

forwardInvocation:方法提供的第二次机会提供了一个这个问题的不太专门的解决方案，并且是动态的而不是静态。它像这样工作：当对象因为它没有匹配消息选择器的方法不能响应消息时，运行时系统通过给对象发送forwardInvocation:消息通知该对象。每个对象都从NSObject类继承了forwardInvocation:方法。但是，NSObject的方法版本只是调用了doesNotRecognizeSelector:。通过覆盖NSObject的版本并实现你自己的，你可以利用forwardInvocation:方法提供的机会转发消息到其他对象。

转发一个消息，所有forwardInvocation:方法需要做的就是：

* 决定消息的去向，并且
* 带着它的原始参数发送

该消息可以通过invokeWithTarget:方法发送：

```
- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    if ([someOtherObject respondsToSelector:
            [anInvocation selector]])
        [anInvocation invokeWithTarget:someOtherObject];
    else
        [super forwardInvocation:anInvocation];
}
```

被转发消息的返回值被返回到原始发送者。可以将所有类型的返回值传递给发件人，包括ids，结构和双精度浮点数。

forwardInvocation:方法就像是未识别的消息的分发中心，将他们分发给不同的接受者。或者它也可以是一个中转站，发送所有的消息到同一个目的地。它可以将一个对象翻译成另一个，或者只是“吞下”一些信息，所以没有回应，也没有错误。forwardInvocation:方法也可以将多个消息合并为一个响应。forwardInvocation:做什么取决于实现。但是，它提供的在一个转发链中链接对象的机会为程序设计提供了可能。

## 转发与多继承（Forwarding and Multiple Inheritance）

## 代理对象（Surrogate Objects）

## 转发和继承（Forwarding and Inheritance）



