# 消息转发（Message Forwarding）

发送一个消息给不处理它的对象是一个错误。然而，在宣布这个错误前，运行时系统给接收对象第二次处理消息的机会。

## 转发（Forwarding）

如果发送一个消息给不处理它的对象，在宣布错误之前运行时给对象发送一个forwardInvocation:消息以及一个NSInvocation对象作为它的唯一参数-NSInvocation对象封装了原始消息以及它传递的参数。

你可以实现forwardInvocation:方法给这个消息一个默认的响应，或者以其他方式避免错误。顾名思义，forwardInvocation:通常被用作转发消息到其他对象。

了解转发的范围和意图，想象下面的场景：首先，假设你设计了一个可以响应名为negotiate的消息的对象，并且你希望它的响应包括另外一类对象的响应。你可以通过在你实现的negotiate方法体中发送negotiate消息给其他对象，轻松的实现这些。

更进一步，假设你希望你的对象对negotiate方法的响应完全是另外一个类中实现的响应。实现这个的一种方式是使你的类从其他类继承该方法。然而，不太可能按照这种方式安排事情。为什么你的类和实现negotiate方法的类在继承层次结构的不同分支中可能有很好的理由。

## 转发与多继承（Forwarding and Multiple Inheritance）

## 代理对象（Surrogate Objects）

## 转发和继承（Forwarding and Inheritance）



