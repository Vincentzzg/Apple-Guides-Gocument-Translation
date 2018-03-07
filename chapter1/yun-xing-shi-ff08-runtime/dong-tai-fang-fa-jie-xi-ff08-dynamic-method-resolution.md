# 动态方法解析（Dynamic Method Resolution）

本章介绍如何动态地提供一个方法的实现。

## 动态方法解析（Dynamic Method Resolution）

有些情况你可能像动态地提供一个方法的实现。例如，Objective-C声明属性的特性（参阅_The Objective-C Programming Language_中的声明属性）包含@dynamic指令：

```
@dynamic propertyName;
```

这告诉编译器该属性关联的方法将会动态地提供。

你可以实现[resolveInstanceMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418500-resolveinstancemethod)和[resolveClassMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418889-resolveclassmethod)方法分别为为一个实例和类方法的给定选择器动态地提供实现。

Objective-C方法是一个最少有两个参数（self和\_cmd）的C函数。可以使用[class\_addMethod](https://developer.apple.com/documentation/objectivec/1418901-class_addmethod)函数给一个类添加函数作为方法。因此，给出下面的函数：

```
void dynamicMethodIMP(id self, SEL _cmd) {
    // implementation ....
}
```

你可以使用_resolveInstanceMethod:_动态地添加它到一个类作为方法（称为_resolveThisMethodDynamically_），像这样：

```
@implementation MyClass
+ (BOOL)resolveInstanceMethod:(SEL)aSEL
{
    if (aSEL == @selector(resolveThisMethodDynamically)) {
          class_addMethod([self class], aSEL, (IMP) dynamicMethodIMP, "v@:");
          return YES;
    }
    return [super resolveInstanceMethod:aSEL];
}
@end
```

转发方法（如[Message Forwarding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)描述的）和动态方法解析在很大程度上是正交的。类有机会再转发机制生效前动态解析一个方法。如果调用[respondsToSelector:](https://developer.apple.com/documentation/objectivec/nsobjectprotocol/1418583-responds)或[instancesRespondToSelector:](https://developer.apple.com/documentation/objectivec/nsobject/1418555-instancesrespondtoselector)，动态方法解析器有机会首先为选择器提供IMP。你如你实现了[resolveInstanceMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418500-resolveinstancemethod)方法但是希望特定的选择器通过转发机制实际转发，则为这些选择器返回NO。

## 动态加载（Dynamic Loading）


