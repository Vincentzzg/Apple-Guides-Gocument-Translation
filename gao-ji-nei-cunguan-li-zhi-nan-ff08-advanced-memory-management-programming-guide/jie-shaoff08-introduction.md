# 关于内存管理

应用程序内存管理是在程序运行时分配内存，使用内存，然后在完成时释放内存这样一个流程。一个写的很好的程序会使用尽可能少的内存。在Objective-C中，它也可以被看做是一种在很多数据和代码间分配有限的内存资源所有权的一种方式。

尽管内存管理总是考虑单独对象层面的问题，你的目标实际上是管理对象图（[object graphs]()）。你应该确保内存中的对象没有比实际需要的更多。

## 预览

Objective-C提供了两种应用程序内存管理方法。

1. 在本指南中描述的称为“手动保留释放”或MRR的方法中，您通过跟踪您拥有的对象来显式管理内存。 这是通过使用称为引用计数的模型实现的，该模型是Foundation类NSObject与运行时环境一起提供的。
2. 在自动引用计数或ARC中，系统使用与MRR相同的引用计数系统，但它会在编译时为您插入适当的内存管理方法调用。 强烈建议您将ARC用于新项目。 如果使用ARC，通常不需要理解本文档中描述的底层实现，尽管它在某些情况下可能会有所帮助。 有关ARC的更多信息，请参阅[_Transitioning to ARC Release Notes_](https://developer.apple.com/library/content/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226)。

### 良好的做法可以防止与内存相关的问题（Good Practices Prevent Memory-Related Problems）

内存管理不正确会导致两种主要问题：

* 释放或覆盖仍在使用的数据
  这会导致内存损坏，并且通常会导致应用程序崩溃，甚至导致用户数据损坏。
* 不释放不再使用的数据会导致内存泄漏
  内存泄漏是分配的内存未被释放的地方，即使它不再被使用。 泄漏导致您的应用程序使用不断增加的内存量，这反过来可能导致系统性能下降或应用程序终止。

然而，从引用计数的角度考虑内存管理通常会适得其反，因为您往往会根据实现细节而不是实际目标来考虑内存管理。相反，您应该从对象所有权和对象图（[object graphs]()）的角度考虑内存管理。

Cocoa使用一个简单的命名约定来表明你何时拥有一个方法返回的对象。

请参阅[Memory Management Policy](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmRules.html#//apple_ref/doc/uid/20000994-BAJHFBGH)。

尽管基本策略很简单，但您可以采取一些实际步骤来简化内存管理，并帮助确保程序的可靠性和可靠性，同时最大限度地减少资源需求。

请参阅[Practical Memory Management](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW1)。

自动释放池块提供了一种机制，您可以向对象发送“延迟”释放消息。这在您想放弃对象所有权但希望避免立即释放它的可能性（例如从方法返回对象）的情况下非常有用。有时你可能会使用自己的自动释放池块。

请参阅[Using Autorelease Pool Blocks](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html#//apple_ref/doc/uid/20000047-CJBFBEDI)。

### 使用分析工具调试内存问题（Use Analysis Tools to Debug Memory Problems）

要在编译时识别代码中的问题，可以使用内置于Xcode中的Clang静态分析器（Clang Static Analyzer）。

如果出现内存管理问题，还可以使用其他工具和技术来识别和诊断问题。

* Technical Note TN2239，[_iOS Debugging Magic_](https://developer.apple.com/library/content/technotes/tn2239/_index.html#//apple_ref/doc/uid/DTS40010638)中介绍了许多工具和技术，特别是使用NSZombie来帮助查找过度释放的对象。
* 您可以使用工具来跟踪引用计数事件并查找内存泄漏。 请参阅[Collecting Data on Your App](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/TheInstrumentsWorkflow.html#//apple_ref/doc/uid/TP40004652-CH5)。



