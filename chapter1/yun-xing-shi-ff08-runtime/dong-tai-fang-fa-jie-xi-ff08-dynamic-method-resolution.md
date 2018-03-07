# 动态方法解析（Dynamic Method Resolution）

本章介绍如何动态地提供一个方法的实现。

## 动态方法解析（Dynamic Method Resolution）

有些情况你可能像动态地提供一个方法的实现。例如，Objective-C声明属性的特性（参阅_The Objective-C Programming Language_中的声明属性）包含@dynamic指令：

```
@dynamic propertyName;
```

这告诉编译器该属性关联的方法将会动态地提供。

你可以实现[resolveInstanceMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418500-resolveinstancemethod)和[resolveClassMethod:](https://developer.apple.com/documentation/objectivec/nsobject/1418889-resolveclassmethod)方法分别为为一个实例和类方法的给定选择器动态地提供实现。







## 动态加载（Dynamic Loading）



