# 实用的内存管理（Practical Memory Management）

尽管[Memory Management Policy](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmRules.html#//apple_ref/doc/uid/20000994-BAJHFBGH)中描述的基本概念是直接了当的，仍然有一些实用的步骤你可以采取以使内存管理更简单，并且帮助你确保你的程序在最小化资源请求的同时保持可靠和强大。

## 使用访问器方法使内存管理更容易

考虑一个需要设置数量的计数对象。

```
@interface Counter : NSObject
@property (nonatomic, retain) NSNumber *count;
@end
```

property声明了两个访问器方法。通常，你应该让编译器合成这些方法；然而，了解它们如何实施是很有益的。

“get”访问器中，你必须返回合成的实例变量，所以不需要retain或release：

```
- (NSNumber *)count {
    return _count;
}
```

“set”方法中，如果其他人都按照相同的规则玩牌，则必须假定新的点数可以随时处置，因此你必须通过发送一个保留（retain）消息获取该对象的所有权--以确保它不会。你也必须通过给它发送一个释放（release）消息放弃对老的数量对象的所有权。（在Objective-C中给nil发送消息是被允许的，所以如果\_count还没有被设置实现依然可以工作。）如果两者是同一个对象，则必须在\[newCount retain\]后发送，你不想无意中导致它被释放。

```
- (void)setCount:(NSNumber *)newCount {
    [newCount retain];
    [_count release];
    // Make the new assignment.
    _count = newCount;
}
```

### 使用访问器方法设置属性值

假设你想实现一个方法来重设计数器。你有几个选择。第一种实现是使用alloc方法创建NSNumber实例，所以你用一个释放来平衡它。

```
- (void)reset {
    NSNumber *zero = [[NSNumber alloc] initWithInteger:0];
    [self setCount:zero];
    [zero release];
}
```

第二种方法是使用使用方便的构造函数创建新的NSNumber对象。因此不需要retain或release消息。

```
- (void)reset {
    NSNumber *zero = [NSNumber numberWithInteger:0];
    [self setCount:zero];
}
```

注意两种方法都是用了设置访问器方法。

下面的代码简单情况下都能正常工作，但是尽可能的避免使用访问器方法，这样做在一些情况下几乎肯定会导致失误（例如，当你忘记保留或释放，或者实例变量的内存管理语义发生变化）。

```
- (void)reset {
    NSNumber *zero = [[NSNumber alloc] initWithInteger:0];
    [_count release];
    _count = zero;
}
```

注意，如果你使用键值观察，那么以这种方式更改变量不符合KVO。

### 在初始化方法和dealloc方法中不要使用访问器方法

唯一不应该使用访问器方法的地方是在初始化方法或dealloc方法中设置一个实例变量。用一个为零的数字对象初始化一个计数器对象，你应该向下面这样实现初始化方法：

```
- init {
    self = [super init];
    if (self) {
        _count = [[NSNumber alloc] initWithInteger:0];
    }
    return self;
}
```

如果允许计数器以非零点数初始化，你应该像下面这样实现一个initWithCount:方法：

```
- initWithCount:(NSNumber *)startingCount {
    self = [super init];
    if (self) {
        _count = [startingCount copy];
    }
    return self;
}
```

由于Counter类有一个对象实例变量，你必须实现dealloc方法。该方法应该通过给它们发送释放消息放弃所有实例变量的所有权，最后它应该调用父类的实现：

```
- (void)dealloc {
    [_count release];
    [super dealloc];
}
```

## 使用弱引用避免循环保持

循环引用的解决方案是使用弱引用。弱引用是一个非拥有的关系，其中源对象不保留其具有引用的对象。

然而，为了保持对象图完整，某处必须要有强引用在（如果只有弱引用，那么文章和段落将没有任何拥有者，将解除分配）。因此，Cocoa建立了约定，一个“父母”对象需要对它的“孩子”保持强引用，而孩子应该对父母保持弱引用。

给你只保持弱引用的对象发送消息时你需要小心。如果你在一个对象解除分配后给它发送消息，你的应用程序将会崩溃。什么时候对象有效必须有明确的条件。大部分情况下，弱引用对象可以注意到其他对象对它的弱引用，循环引用也一样，当它解除分配时有责任通知其他对象。例如，当你向通知中心注册一个对象时，通知中心存储对象的一个弱引用，当合适的通知发送时发送消息给该对象。当对象解除分配时，你应该取消它在消息中心的注册以防止消息中心给不再存在的对象发送进一步的消息。同样，当一个委托对象解除分配，你应该通过给其他对象发送一个带nil参数的setDelegate消息来移除委托关系。这些消息通常在对象的dealloc方法中发送。

## 避免导致你正在使用的对象解除分配

Cocoa的所有权策略指出收到的对象在调用方法的整个范围内应该总是保持有效。从当前范围内返回一个收到的对象而不用担心它被释放也是可能的。不用关心你的应用程序一个对象的getter方法返回一个缓存的实例变量或计算值。要紧的是当你需要时对象保持有效。

这条规则偶尔有例外，主要分为两类。

1. 当对象被从一个基本集合类（[collection classes]()）中移出。

   ```
   heisenObject = [array objectAtIndex:n];
   [array removeObjectAtIndex:n];
   // heisenObject could now be invalid.
   ```

   当一个对象被从一个基本集合类中移出，它将收到一个release消息。如果这个集合是被移出对象的唯一一个拥有者，这个移出的对象会马上解除分配。

2. 当一个“父对象”被解除分配

   一些情况下，你从另一个对象中检索一个对象，然后直接或间接地释放了父对象。如果释放的父对象导致其被解除分配，且这个父对象是该子对象的唯一拥有者，则该子对象将同时被解除分配。

为了避免这些情况，你应该在接收时保留heisenObject对象，并在完成时释放它。例如：

```
heisenObject = [[array objectAtIndex:n] retain];
[array removeObjectAtIndex:n];
// Use heisenObject...
[heisenObject release];
```

## 不要使用dealloc来管理稀缺资源

## 集合拥有它包含的对象

当你添加一个对象到集合（如数组，字典，或集合），这个集合就拥有该对象。当对象被从集合中移除或集合自己释放时，集合将放弃所有权。因此，例如，如果你想创建一个数字数组，你应该执行以下任一操作：

```
NSMutableArray *array = <#Get a mutable array#>;
NSUInteger i;
// ...
for (i = 0; i < 10; i++) {
    NSNumber *convenienceNumber = [NSNumber numberWithInteger:i];
    [array addObject:convenienceNumber];
}
```

这种情况，你没有调用分配（alloc）方法，所以不需要调用release。也不需要保留新数字（convenienceNumber），因为数组会这样做。

```
NSMutableArray *array = <#Get a mutable array#>;
NSUInteger i;
// ...
for (i = 0; i < 10; i++) {
    NSNumber *allocedNumber = [[NSNumber alloc] initWithInteger:i];
    [array addObject:allocedNumber];
    [allocedNumber release];
}
```

这种情况，你的确需要在for循环内给allocNumber发送一个释放消息来平衡alloc。由于当它被使用addObject：添加时数组保会留数字，所以当它在数组中时将不会被解除分配。

要理解这一点，请将自己置于实施集合班的人员的位置。 你想要确保没有任何对象给你照看之后消失，所以当你传递给他们一个保留消息。如果他们被删除，你必须发送一个平衡释放消息， 并且在你自己的dealloc方法里应该给所保留的对象发送一个释放消息。

## 所有权策略是通过保留计数实现的（Ownership Policy Is Implemented Using Retain Counts）

所有权策略是通过引用技术实现的--通常在保留方法后被称为“保留计数”。每一个对象都有一个引用计数。

* 当你创建一个对象，它有一个保留计数1。
* 当你给一个对象发送一个保留（retain）消息，它的保留计数加1。
* 当你给一个对象发送一个释放（release）消息，它的保留计数减1。
  当你给一个对象发送自动释放（autorelease）消息，它的保留计数在当前自动释放池块结束时减1。

* 如果一个对象的保留计数减少到零，它将被解除分配。

> 重点：没有理由需要显示的请求一个对象的保留计数是多少（参阅[retainCount](https://developer.apple.com/documentation/objectivec/1418956-nsobject/1571952-retaincount)）。结果往往是误导，因为你可能没有注意到哪些框架对象保留了你感兴趣的对象。调试内存管理问题时，你需要考虑的仅仅是确保你的代码遵循所有权规则。



