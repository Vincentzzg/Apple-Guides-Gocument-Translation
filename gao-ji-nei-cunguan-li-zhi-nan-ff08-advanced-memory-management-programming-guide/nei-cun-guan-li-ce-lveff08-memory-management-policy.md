# 内存管理策略（Memory Management Policy）

引用计数环境下的内存管理使用的基本模型是通过NSObject协议和标准方法命名约定组合提供的。NSObject类也定义了方法dealloc，它是在对象重新分配时自动被调用。这个文章描述了你需要知道的Cocoa程序内存管理相关的所有基础规则，并且提供了一些正确用法的例子。

## 基础内存管理规则

内存管理模型基于对象所有权。任何对象都可能有一个或更多所有者。只要一个对象有至少一个所有者，它就会继续存在。如果对象没有所有者，运行时系统会自动销毁它。为了弄清楚什么时候你拥有一个对象什么时候你不在拥有，Cocoa设置了下面的策略：

* **你拥有任何你创建的对象**  
  使用名字以“alloc”、“new”、“copy”或“mutableCopy”（例如，alloc、newObject或mutableCopy）开头的方法创建对象。

* **你可以使用保留（retain）取得对象的所有权  
  **接收的对象通常保证在接收它的方法内保持可用，并且方法也可以安全的返回这个对象给它的调用者。两种情况下使用retain：（1）访问器方法声明或初始化（init）方法，获取你想存储为属性值的对象的所有权；（2）防止某个对象作为其他操作的副作用而失效（像[Avoid Causing Deallocation of Objects You’re Using](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/20000043-1000922)中解释的一样）。

* **当你不再需要时，你必须放弃你拥有的对象的所有权  
  **通过给对方发送release消息或autorelease消息放弃所有权。因此，在Cocoa术语中，放弃对象的所有权通常被称为“释放”对象。

* **你不得放弃你不拥有的对象的所有权**
  这是前面政策的必然结果，明确表示。

### 一个简单的例子

### 使用autorelease发送延迟的释放

### 你不拥有引用返回的对象

## 实现dealloc来放弃对象的所有权

## 核心基础使用类似但不同的规则



