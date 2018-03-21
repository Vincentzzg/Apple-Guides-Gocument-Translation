# 定制现有类（Customizing Existing Classes）

## 分类（Categories）

分类的定义使用_@interface_关键字，就像标准Objective-C类描述一样，但是没有声明任何的子类继承。相反，在括号里指定了分类的名称，如下：

```
@interface ClassName (CategoryName)

@end
```

### 使用分类给现有类添加方法

在分类中定义的方法原始类的所有实例都可以使用，原始类的分类也可以。在运行时，分类中添加的方法和原始类里面实现的方法没有区别。

像给现有的类添加方法一样，你也可以使用分类把一个复杂类里面的实现分到几个源码文件里。

分类一般被用作声明实例方法或类方法，但是通常不适合声明额外的属性。在分类中包含属性声明是合法的语法，但是不能在分类中声明额外的实例变量。这意味着编译器不会合成任何实例变量，也不会合成任何属性访问器方法。你可以在分类实现里写自己的访问器方法，但是除非它已经被原始类存储，否则你将无法跟踪该属性的值。

### 避免分类方法名冲突

如果一个工程里面有同一个类的两个分类里面有两个方法名一样的方法，在运行时，只有一个方法实现能胜出并被添加到原始类上，但是具体是哪一个是不一定的。

还有一种情况就是给Cocoa或Cocoa Touch中的类添加方法，然而这个方法在后面的版本里面被添加到了原始类里，这个时候在新的系统版本上你就需要处理命名冲突。

为了避免未定义的行为，最好是在框架类的分类中给方法名添加一个前缀，就像我们给自己的类名添加的前缀一样。可以选择使用类名前缀一样的三个字母，但是需要小写以遵循方法名的常规约定，然后在剩下的方法名前使用下划线。像这样：

```
@interface NSSortDescriptor (XYZAdditions)
+ (id)xyz_sortDescriptorWithKey:(NSString *)key ascending:(BOOL)ascending;
@end
```

## 类扩展（Class Extensions）

### 类扩展扩展了内部实现

```
类扩展跟分类有些相似，但它只能被添加到编译时有源码的类中（类跟类扩展同时编译）。类扩展里面声明的方法需要在原始类的@implementation部分实现，因此，不能给框架类添加扩展，比如像NSString这样的Cocoa或Cocoa Touch类。
```

声明类扩展的语法跟分类的语法很像，像这样：

```
@interface ClassName ()

@end
```

因为括号里没有名字，类扩展通常被称为匿名分类。

与常规类别不同，类扩展可以给类添加它自己的属性和实例变量。如果在类扩展里声明属性，可以这样：

```
@interface XYZPerson ()
@property NSObject *extraProperty;
@end
```

编译器会在主类实现中自动合成相关的存取方法以及一个实例变量。

如果你在类扩展里添加了任何方法，必须在类的主要实现中实现这些方法。

也可以使用类扩展添加自定义的实例变量。

这些在类扩展的大括号中声明：

```
@interface XYZPerson () {
    id _someCustomInstanceVariable;
}
...
@end
```

### 使用类扩展隐藏私有信息

一个类的主接口用于定义其他类可以与其进行的交互方式。换句话说，它是这个类的公共接口。

类扩展通常被用于通过提供类内部使用的额外的私有方法或属性来扩展公共接口。例如，在接口中将属性定义为只读，但是为了使内部方法可以直接修改其属性，在实现之前在类扩展中定义为可读可写。

例如：

```
@interface XYZPerson : NSObject
...
@property (readonly) NSString *uniqueIdentifier;
- (void)assignUniqueIdentifier;
@end
```

这意味着uniqueIdentifier不可能被外部的其他对象直接设置。如果一个人还没有，必须通过调用assignUniqueIdentifier方法请求分配一个标识符。

为了XYZPerson类能在内部修改修改这个属性，在实现文件顶部定义的类扩展中重新声明这个属性是有意义的。

```
@interface XYZPerson ()
@property (readwrite) NSString *uniqueIdentifier;
@end

@implementation XYZPerson
...
@end
```

> 注意：_readwrite_关键字是可选的，因为它是默认的。为了清晰起见，你可能会在重复声明的时候使用它。

这意味着编译器现在还是会合成一个setter方法，所以XYZPerson实现中的任何方法都能直接使用setter方法或者点语法设置属性。

通过在XYZPerson实现源码文件中声明类扩展，信息对XYZPerson保持私有。如果其他对象试图设置这个属性，编译器会报错。

> 注意：通过添加上面展示的类扩展，重新声明_uniqueIdentifier_属性为readwrite属性，无论其他源代码文件是否知道类扩展，每一个XYZPerson对象在运行时_setUniqueIdentifier:_方法都存在。
>
> 如果在这些源码文件里有代码试图调用私有方法或者设置一个readonly属性编译器会报错，但是可以避免编译器报错，并利用动态运行时特性以其他方式调用这些方法，比如使用NSObject提供的performSelector:...方法中的一个。你应该避免一个类层次结构或设计；相关，类接口应该总是定义正确的“public”交互。
>
> 如果你想让私有方法和属性对选定的类可用，比如框架中的相关类，你可以在单独的文件中声明类扩展并在需要引用的源码中引用它。一个类有两个头文件并不稀罕，例如XYZPerson.h和XYZPersonPrivate.h。当你发布框架的时候，只需要发布一个公共XYZPerson.h头文件。

## 考虑类自定义的替代方案

_分类（Categories）_和_类扩展（Class Extensions）_方便了直接给现有类添加添加行为，但是有些时候这并不是最好的选择。

与其对布局和内容进行硬编码决定，另一种方法是利用继承，并将这些决策留在专门设计的方法中，以便被子类覆盖。虽然这确实使得重用类相对容易，但是每次需要使用原始类时仍然需要创建一个新的子类。

另外一种替代方案是为类使用_delegate_对象。任何可能限制可重用性的决定都可以委托给另一个对象，把这些决定留到运行时。一个常见的例子是标准表格视图类（OSX中的NSTableView，iOS中的UITableView）。为了是一个通用的表格视图（使用一个或多个列或行显示信息的对象）有用，它把关于内容的决定留给了运行时的其他对象。

_绑定引用（associative reference）_

