# 关于内存管理

应用程序内存管理是在程序运行时分配内存，使用内存，然后在完成时释放内存这样一个流程。一个写的很好的程序会使用尽可能少的内存。在Objective-C中，它也可以被看做是一种在很多数据和代码间分配有限的内存资源所有权的一种方式。

尽管内存管理总是考虑单独对象层面的问题，你的目标实际上是管理对象图（[object graphs]()）。你应该确保内存中的对象没有比实际需要的更多。

## 预览

Objective-C提供了两种应用程序内存管理方法。

1. 


### 良好的做法可以防止与内存相关的问题（Good Practices Prevent Memory-Related Problems）

### 使用分析工具调试内存问题（Use Analysis Tools to Debug Memory Problems）

要在编译时识别代码中的问题，可以使用内置于Xcode中的Clang静态分析器（Clang Static Analyzer）。

如果出现内存管理问题，还可以使用其他工具和技术来识别和诊断问题。

* Technical Note TN2239，[_iOS Debugging Magic_](https://developer.apple.com/library/content/technotes/tn2239/_index.html#//apple_ref/doc/uid/DTS40010638)中介绍了许多工具和技术，特别是使用NSZombie来帮助查找过度释放的对象。
* 您可以使用工具来跟踪引用计数事件并查找内存泄漏。 请参阅[Collecting Data on Your App](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/TheInstrumentsWorkflow.html#//apple_ref/doc/uid/TP40004652-CH5)。



