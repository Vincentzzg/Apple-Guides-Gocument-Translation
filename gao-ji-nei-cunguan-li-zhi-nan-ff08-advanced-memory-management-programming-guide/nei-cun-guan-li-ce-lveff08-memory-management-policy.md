# 内存管理策略（Memory Management Policy）

引用计数环境下的内存管理使用的基本模型是通过NSObject协议和标准方法命名约定组合提供的。NSObject类也定义了方法dealloc，它是在对象被释放时自动被调用。这个文章描述了你需要知道的Cocoa程序内存管理相关的所有基础规则，并且提供了一些正确用法的例子。

## 基础内存管理规则

内存管理模型基于对象所有权。任何对象都可能有一个或更多所有者。只要一个对象有至少一个所有者，它就会继续存在。如果对象没有所有者，运行时系统会自动销毁它。为了弄清楚什么时候你拥有一个对象什么时候你不拥有对象，Cocoa设置了下面的策略：

* **你拥有任何你创建的对象**  
  使用名字以“alloc”、“new”、“copy”或“mutableCopy”（例如，alloc、newObject或mutableCopy）开头的方法创建对象。

* **你可以使用保留（retain）取得对象的所有权                      
  **接收的对象通常保证在接收它的方法内保持可用，并且方法也可以安全的返回这个对象给它的调用者。两种情况下使用retain：（1）访问器方法声明或初始化（init）方法，获取你想存储为属性值的对象的所有权；（2）防止某个对象作为其他操作的副作用而失效（像[Avoid Causing Deallocation of Objects You’re Using](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/20000043-1000922)中解释的一样）。

* **当你不再需要时，你必须放弃你拥有的对象的所有权                      
  **通过给对方发送release消息或autorelease消息放弃所有权。因此，在Cocoa术语中，放弃对象的所有权通常被称为“释放”对象。

* **你不得放弃你不拥有的对象的所有权**  
  这是前面政策的必然结果，明确表示。

### 一个简单的例子

为了说明策略，请考虑以下代码片段：

```
{
    Person *aPerson = [[Person alloc] init];
    // ...
    NSString *name = aPerson.fullName;
    // ...
    [aPerson release];
}
```

Person对象是使用alloc方法创建的，所以当它不再被需要时随后被发送了一个释放消息。person对象的名字不是使用任何拥有方法检索的，所以它不会发送释放消息。尽管如此，注意，例子使用的是release而不是autorelease。

### 使用autorelease发送延迟的释放

当你需要发送一个延迟的释放消息时使用autorelease，通常是从方法中返回一个对象。例如，你可以像这样实现fullName方法：

```
- (NSString *)fullName {
    NSString *string = [[[NSString alloc] initWithFormat:@"%@ %@",
                                          self.firstName, self.lastName] autorelease];
    return string;
}
```

你拥有alloc方法返回的字符串。为了遵守内存管理规则，你必须在失去对字符串的引用之前放对它的所有权。然而，如果使用release，该字符串会在返回前解除分配（方法将返回一个无效对象）。使用autorelease，表示你想要放弃所有权，但是你允许方法的调用者在它解除分配前使用该返回的字符串。

你也可以向下面这样实现fullName方法：

```
- (NSString *)fullName {
    NSString *string = [NSString stringWithFormat:@"%@ %@",
                                 self.firstName, self.lastName];
    return string;
}
```

根据基本规则，你不拥有stringWithFormat：返回的字符串，所以你可以从方法中安全的返回字符串。

作为对比，下面的实现是错的：

```
- (NSString *)fullName {
    NSString *string = [[NSString alloc] initWithFormat:@"%@ %@",
                                         self.firstName, self.lastName];
    return string;
}
```

根据命名约定，没有什么可以表示fullName方法的调用者拥有返回的字符串。因此调用者没有理由释放返回的字符串，它会因此泄漏。

### 你不拥有引用返回的对象

Cocoa中的一些方法指定一个对象对象是通过引用返回的（也就是说，它们接收一个ClassName \*\*或id \*类型的参数）。一种常见模式是如果发生错误时使用一个NSError对象包含错误相关的信息，如[initWithContentsOfURL:options:error:](https://developer.apple.com/documentation/foundation/nsdata/1407864-init)\(NSData\)和[initWithContentsOfFile:encoding:error:](https://developer.apple.com/documentation/foundation/nsstring/1412610-initwithcontentsoffile)\(NSString\)所示。

这种情况下， 适用的规则与已描述的相同。当你调用任何这些方法，你没有创建NSError对象，所以你不拥有它。因此不需要释放它，如本例所示：

```
NSString *fileName = <#Get a file name#>;
NSError *error;
NSString *string = [[NSString alloc] initWithContentsOfFile:fileName
                        encoding:NSUTF8StringEncoding error:&error];
if (string == nil) {
    // Deal with error...
}
// ...
[string release];
```

## 实现dealloc来放弃对象的所有权

NSObject定义了一个方法，dealloc，当一个对象没有拥有者并且它的内存被回收时自动调用，在Cocoa术语中它就是“释放”或“解除分配”。dealloc方法的角色是释放对象拥有的内存，并处置其拥有的任何资源，包括任何对象实例变量的拥有权。

下面的例子说明了你应该如何为Person类实现dealloc方法：

```
@interface Person : NSObject
@property (retain) NSString *firstName;
@property (retain) NSString *lastName;
@property (assign, readonly) NSString *fullName;
@end

@implementation Person
// ...
- (void)dealloc
    [_firstName release];
    [_lastName release];
    [super dealloc];
}
@end
```

> 重点：永远不要直接调用另外一个对象的dealloc方法。
>
> 必须在实现的最后调用父类的实现。
>
> 你不应该将系统资源的管理与对象生命周期联系起来；参阅[Don’t Use dealloc to Manage Scarce Resources](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW13)。
>
> 当应用程序终止时，对象将不会接收到dealloc消息。因为进程的内存在退出时会自动清空，允许操作系统清理资源比自己调用所有的内存管理方法要更简单有效。

## 核心基础使用类似但不同的规则

Core Foundation对象有类似的内存管理规则（请参阅[_Memory Management Programming Guide for Core Foundation_](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/CFMemoryMgmt.html#//apple_ref/doc/uid/10000127i)）。 然而，Cocoa和Core Foundation的命名约定是不同的。 特别是，Core Foundation的创建规则（请参阅[The Create Rule](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)）不适用于返回Objective-C对象的方法。 例如，在下面的代码片段中，您不负责放弃myInstance的所有权：

```
MyClass *myInstance = [MyClass createInstance];
```



