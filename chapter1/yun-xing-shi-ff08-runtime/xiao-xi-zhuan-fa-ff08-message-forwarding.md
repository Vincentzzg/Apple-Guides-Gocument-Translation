# 消息转发（Message Forwarding）

发送一个消息给不处理它的对象是一个错误。然而，在宣布这个错误前，运行时系统给接收对象第二次处理消息的机会。

## 转发（Forwarding）

如果发送一个消息给不处理它的对象，在宣布错误之前运行时给对象发送一个forwardInvocation:消息以及一个NSInvocation对象作为它的唯一参数-NSInvocation对象封装了原始消息以及它传递的参数。

## 转发与多继承（Forwarding and Multiple Inheritance）

## 代理对象（Surrogate Objects）

## 转发和继承（Forwarding and Inheritance）



