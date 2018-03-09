# 消息转发（Message Forwarding）

发送一个消息给不处理它的对象是一个错误。然而，在宣布这个错误前，运行时系统给接收对象第二次处理消息的机会。

## 转发（Forwarding）

如果发送一个消息给不处理它的对象，在宣布错误之前运行时给对象发送一个_forwardInvocation:_消息以及一个_NSInvocation_对象作为它的唯一参数-_NSInvocation_对象封装了原始消息以及它传递的参数。

你可以实现_forwardInvocation:_方法给这个消息一个默认的响应，或者以其他方式避免错误。顾名思义，_forwardInvocation:_通常被用作转发消息到其他对象。

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

_forwardInvocation:_方法提供的第二次机会提供了一个这个问题的不太专门的解决方案，并且是动态的而不是静态。它像这样工作：当对象因为它没有匹配消息选择器的方法不能响应消息时，运行时系统通过给对象发送_forwardInvocation:_消息通知该对象。每个对象都从_NSObject_类继承了_forwardInvocation:_方法。但是，_NSObject_的方法版本只是调用了_doesNotRecognizeSelector:_。通过覆盖_NSObject_的版本并实现你自己的，你可以利用_forwardInvocation:_方法提供的机会转发消息到其他对象。

转发一个消息，所有_forwardInvocation:_方法需要做的就是：

* 决定消息的去向，并且
* 带着它的原始参数发送

该消息可以通过_invokeWithTarget:_方法发送：

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

_forwardInvocation:_方法就像是未识别的消息的分发中心，将他们分发给不同的接受者。或者它也可以是一个中转站，发送所有的消息到同一个目的地。它可以将一个对象翻译成另一个，或者只是“吞下”一些信息，所以没有回应，也没有错误。_forwardInvocation:_方法也可以将多个消息合并为一个响应。_forwardInvocation:_做什么取决于实现。但是，它提供的在一个转发链中链接对象的机会为程序设计提供了可能。

> 注意：_forwardInvocation:_方法只有在它们标称接收方法中的现有方法是才会处理消息。例如，如果你希望对象转发_negotiate_消息到另一个对象，则它自己不能拥有_negotiate_方法。如果它有，则消息永远不会抵达_forwardInvocation:_。

更多关于转发和调用的信息，参阅基础框架参考（Foundation framework reference）中的NSInvocation类规范。

## 转发与多继承（Forwarding and Multiple Inheritance）

转发模仿继承，并可用于将多继承的一些效果提供给Objective-C编程。通过转发来响应消息的对象视乎借用或“继承”了另一个类中定义的方法实现，如下图所示：

这个例子中，Warrior类的实例转发一个negotiate消息到Diplomat类的实例。战士（Warrior）视乎会像外交官（Diplomat）一样进行谈判（negotiate）。它视乎对negotiate消息做出了响应，并且出于所有实际目的，它确实做出了响应（尽管它确实是一名外交官在做这项工作）。

转发消息的对象因此从继承层次结构中的两个分支”继承“了方法--它自己的分支以及响应消息的对象的分支。上面的例子中，好像Warrior类继承Diplomat作为它自己的超类。

转发提供了大部分多继承的特性。但是，两者之间有一个重要的区别：多继承合并不同的能力到一个单独的类中。它倾向于大型，多面的物体。另一方面，转发将单独的责任分配给不同的对象。它分解问题到更小的对象，但以对消息发送者透明的方式关联这些对象。

## 代理对象（Surrogate Objects）

转发不仅仅模仿多继承，它还可以开发代表或”覆盖“更多实体对象的轻量级对象。代理人代表另一个对象并向其发送消息。

_The Objective-C Programming Language_中的”远程消息“中讨论的代理就是这样的代理。代理负责转发消息到远程接受者的管理细节，确保参数值在连接中被拷贝和检索，等等。但是它不视图做其他事情；它不会复制远程对象的功能，而只是给远程对象一个本地地址，这是一个可以在另一个应用程序中接收消息的地方。

其他类型的代理对象也是可能的。例如，假设你有一个操作很多数据的对象--也许它创建一个复杂的图片或读取磁盘上的文件内容。设置该对象是耗时的，所以你倾向于懒惰地去做--当它正好被需要或者系统资源暂时闲置时。同时，为了使应用程序中的其他对象正常工作，你至少需要此对象的占位符。

在这种情况下，你可以为它初始化创建，不是完整的对象，而是一个轻量级代理。该对象可以自己做一些事情，例如回答关于数据的问题，但是大部分时候它仅仅为大的对象持有一个位置，当实际到达，转发消息给它。当代理的forwardInvocation:方法第一次接收到注定是其他对象的消息时，它会确保对象存在并且它不存在时创建它。对于较大对象的所有消息都通过代理，所以就程序的其余部分而言，代理和较大的对象将是相同的。

## 转发和继承（Forwarding and Inheritance）

尽管转发模仿继承，NSObject类永远不会混淆这两者。像respondsToSelector:和isKindOfClass:这些方法，金查看继承层次结构，从不查看转发链。例如，如果一个Warrior对象被询问它是否响应negotiate方法，

```
if ( [aWarrior respondsToSelector:@selector(negotiate)] )
    ...
```

答案是NO，尽管从某种意义上说，它可以无错地接收negotiate消息并且响应它们，通过转发它们给Diplomat类。

一些情况下，NO是对的答案。但也不一定。如果你使用转发来设置代理对象或者拓展类的功能，则转发机制应该与继承一样透明。如果你希望你的对象表现的好像它们真的继承了它们转发消息的对象的行为一样，你需要重新实现respondsToSelector:和isKindOfClass:方法以包括你的转发算法：

```
- (BOOL)respondsToSelector:(SEL)aSelector
{
    if ( [super respondsToSelector:aSelector] )
        return YES;
    else {
        /* Here, test whether the aSelector message can     *
         * be forwarded to another object and whether that  *
         * object can respond to it. Return YES if it can.  */
    }
    return NO;
}
```

respondsToSelector:和isKindOfClass:之外，instancesRespondToSelector:方法也应该镜像转发算法。如果使用了协议，conformsToProtocol:方法同样应该添加到列表中。同样，如果一个对象转发了任何它接收的远程消息，它应该实现methodSignatureForSelector:方法，它可以返回最终响应转发的消息的方法的准确描述；例如，如果对象能够转发消息给它的代理，你应该像下面这样实现methodSignatureForSelector:：

```
- (NSMethodSignature*)methodSignatureForSelector:(SEL)selector
{
    NSMethodSignature* signature = [super methodSignatureForSelector:selector];
    if (!signature) {
       signature = [surrogate methodSignatureForSelector:selector];
    }
    return signature;
}
```

你可能考虑将转发算法放在私密代码的某个位置，并使用所有这些方法（包括forwardInvocation:方法），将其调用。

> 注意：这是一个高级技术，仅适用在其他解决方案都不可能的情况下。它不打算作为继承的替代。如果你必须适用该技术，确保你完全理解做转发的类和你要转发的类的行为。

本节中提到的方法在Foundation框架参考（Foundation framework reference）中的NSObject类规范中进行了描述。 有关invokeWithTarget的信息，请参阅Foundation框架参考（Foundation framework reference）中的NSInvocation类规范。

