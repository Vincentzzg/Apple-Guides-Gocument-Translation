# 使用自动释放池块（Using Autorelease Pool Blocks）

自动释放池块提供了一个机制，因此你可以释放一个对象的所有权，但是避免其被立刻解除分配的可能性（例如当你从一个方法返回对象）。通常，你不需要创建自己的自动释放池，但是有些情况你必须这样做或者这样做是有益的。

## 关于自动释放池块

一个自动释放池块使用@autorelease标记，像下面例子描述的一样：

```
 @autoreleasepool {
    // Code that creates autoreleased objects.
}
```

自动释放池块结束的时候，块内收到自动释放消息的对象会收到一个释放消息，在块内对象每次收到收到自动释放消息都会收到释放消息。

像其他代码块一样，自动释放池块也可以嵌套：

```
@autoreleasepool {
    // . . .
    @autoreleasepool {
        // . . .
    }
    . . .
}
```

（你应该看不到像上面这样的代码；通常一个源文件中自动释放池块中的代码会调用另外一个源文件中包含在其他自动释放池中的代码）。对于给定的自动释放消息，相应的释放消息在自动释放消息发送的那个自动释放池块的结尾发送。

Cocoa总是期望代码在自动释放池块内执行，否则自动释放的对象将不会释放，而你的应用程序将内存泄漏。（如果你在自动释放池块外发送一个自动释放消息，Cocoa记录一个合适的错误日志。）AppKit和UIKit框架在自动释放池块内处理每一个时间循环迭代（如鼠标停止事件或轻击）。因此你通常不用自己创建自动释放池块，甚至可以看到用于创建一个的代码。然而，有三种情况你可能需要使用你自己的自动释放池块：

1. 如果你在写一个不基于UI框架的程序，比如一个命令行工具。
2. 如果你写了一个创建大量临时对象的循环  
   你可能在循环内使用自动释放池块在下一个迭代前处理这些对象。在循环内使用自动释放池块帮助减少应用程序的最大内存占用量。

3. 如果你产生了一个辅助线程。  
   你必须在线程一开始执行就创建你自己的自送释放池块；否则，你的应用程序将泄漏对象。（有关详细信息，请参阅[Autorelease Pool Blocks and Threads](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html#//apple_ref/doc/uid/20000047-1041876)。）

## 使用本地自动释放池块来降低峰值内存占用

许多程序创建自动释放的临时对象。这些对象添加到程序的内存占用空间直到块结束。许多情况下，允许临时对象积累知道当前事件循环迭代结束不会导致过度的开销；然而一些情况，你可能创建大量的临时对象基本上都添加到内存占用，而且你向更快速的处理。后面一种情况，你可以创建自己的自动释放池块。在块的末尾，临时对象被释放，这通常会导致其释放，从而减少程序的内存占用。

下面的例子展示了你应该怎样为一个for循环使用本地自动释放池块。

```
NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {
 
    @autoreleasepool {
        NSError *error;
        NSString *fileContents = [NSString stringWithContentsOfURL:url
                                         encoding:NSUTF8StringEncoding error:&error];
        /* Process the string, creating and autoreleasing more objects. */
    }
}
```

for循环每次处理一个文件。自动释放池块内任何发送自动释放消息的对象将会在块的末尾释放。

在自动释放池块后，你应该将在块内自动释放的任何对象视为“已处理”。不要给这些对象发送消息或将其返回给方法调用者。如果你必须在自动释放池块外使用临时对象，你可以通过在块内发送一个保留消息给该对象，然后在块后给它发送自动释放消息，如下例子所示：

```
– (id)findMatchingObject:(id)anObject {
 
    id match;
    while (match == nil) {
        @autoreleasepool {
 
            /* Do a search that creates a lot of temporary objects. */
            match = [self expensiveSearchForObject:anObject];
 
            if (match != nil) {
                [match retain]; /* Keep match around. */
            }
        }
    }
 
    return [match autorelease];   /* Let match go and return it. */
}
```

发送retain以匹配autorelease池内的autorelease池会阻止并发送autorelease给autorelease池后的autorelease池块扩展匹配的生存期并允许它在循环外接收消息并返回给findMatchingObject：的调用者。

## 自动释放池块和线程



