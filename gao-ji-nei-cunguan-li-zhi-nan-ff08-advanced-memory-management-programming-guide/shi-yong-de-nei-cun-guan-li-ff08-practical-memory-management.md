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

## 避免导致你正在使用的对象解除分配

## 不要使用dealloc来管理稀缺资源

## 集合拥有它包含的对象

## 使用保留计数实现所有权策略



