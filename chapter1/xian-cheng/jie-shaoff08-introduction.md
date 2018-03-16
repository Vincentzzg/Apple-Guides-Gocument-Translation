# 介绍（Introduction）

并发是多个事务在同一时间发生的概念。随着多核CPU的增加以及每个处理器里面的核心个数将会一直增加的认识，软件开发者需要新的方式以利用它们。尽管像OS X和iOS这样的操作系统有并行运行多个程序的能力，大部分程序运行在后台并且执行需要小的连续处理器时间的事务。当前前台的应用程序不仅捕获了用户的注意力也让处理器很忙。如果应用程序有许多工作要做，但是只使用了一小部分可用的核心，那些额外的处理资源被浪费了。

过去，采用并行到应用程序中需要创建一个或多个额外的线程。不幸的是，编写线程代码是一个挑战。线程是必须手动管理的底层工具。鉴于对于一个应用程序最佳的线程数会根据当前系统的加载以及底层硬件动态变化，如果不可能实现的话，实现一个正确的线程解决方案变得非常困难。另外，通常跟线程一起使用的同步机制增加了复杂度和风险，而没有任何提高性能的保证。

对于并行任务的执行，OS X和iOS都采用了比传统基于线程的系统和应用程序更异步的方式。不是直接创建线程，应用程序只需要定义特定的任务然后让系统执行它们。通过让系统管理线程，应用程序获得了使用线程不能获得的扩展性。应用程序开发者也获得了更简单和更高效的编程模型。

本文档描述了您应该用来在应用程序中实现并发的技术和技术。 本文档中描述的技术在OS X和iOS中均可用。

## 文档的组织（Organization of This Document）

本文档包含下列章节：

* 并发和应用程序（[Concurrency and Application Design](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/ConcurrencyandApplicationDesign/ConcurrencyandApplicationDesign.html#//apple_ref/doc/uid/TP40008091-CH100-SW1)）介绍了异步应用程序设计的基础以及异步执行你的自定义任务的技术。
* 操作队列（[Operation Queues](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW1)）展示了使用Objective-C对象封装和执行任务的方法。
* 调度队列（[Dispatch Queues](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW1)）展示了在基于C的应用程序中如何并行执行任务。
* 调度来源（[Dispatch Sources](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html#//apple_ref/doc/uid/TP40008091-CH103-SW1)）展示了如何异步处理系统事件。
* 从线程迁移（[Migrating Away from Threads](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/ThreadMigration/ThreadMigration.html#//apple_ref/doc/uid/TP40008091-CH105-SW1)）提供了迁移现有基于线程的代码到使用更新技术的技术。

文档也包括了定义相关术语的词汇表。

## 关于属于的说明（A Note About Terminology）

## 参考（See Also）



