# 消息转发（Message Forwarding）

发送一个消息给不处理它的对象是一个错误。然而，在宣布这个错误前，运行时系统给接收对象第二次处理消息的机会。

## 转发（Forwarding）

如果发送一个消息给不处理它的对象，在宣布错误之前运行时给对象发送一个forwardInvocation:消息以及一个NSInvocation对象作为它的唯一参数-NSInvocation对象封装了原始消息以及它传递的参数。

你可以实现forwardInvocation:方法给这个消息一个默认的响应，或者以其他方式避免错误。顾名思义，forwardInvocation:通常被用作转发消息到其他对象。



## 转发与多继承（Forwarding and Multiple Inheritance）

## 代理对象（Surrogate Objects）

## 转发和继承（Forwarding and Inheritance）



