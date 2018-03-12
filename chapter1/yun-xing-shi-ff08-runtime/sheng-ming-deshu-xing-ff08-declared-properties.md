# 声明的属性（Declared Properties）

当编译器碰到属性声明时（参阅_The Objective-C Programming Language_中的属性声明），它会生成与封装类、类别或协议相关联的描述性元数据。你可以通过使用支持通过名字在类或协议中查询属性的功能访问这些元数据，以@encode字符串的形式获取属性的类型。，并将属性的属性列表复制为C字符串数组。以生命属性的列表可用于每个类和协议。

## 属性类型和功能（Property Type and Functions）

Property结构定义了属性描述符的不透明句柄。

```
typedef struct objc_property *Property;
```

你可以使用class\_copyPropertyList和protocol\_copyPropertyList函数分别获得跟类（包括加载的类别）和协议关联的属性数组：

```
objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount)
objc_property_t *protocol_copyPropertyList(Protocol *proto, unsigned int *outCount)
```

例如，给出下面的类声明：

```
@interface Lender : NSObject {
    float alone;
}
@property float alone;
@end
```

你可以获得属性列表，通过：

```
id LenderClass = objc_getClass("Lender");
unsigned int outCount;
objc_property_t *properties = class_copyPropertyList(LenderClass, &outCount);
```

你可以使用property\_getName函数发现属性的名称：

```
const char *property_getName(objc_property_t property)
```

你可以使用class\_getProperty和protocol\_getProperty函数通过名称分别获得类和协议中属性的引用：

```
objc_property_t class_getProperty(Class cls, const char *name)
objc_property_t protocol_getProperty(Protocol *proto, const char *name, BOOL isRequiredProperty, BOOL isInstanceProperty)
```

你可以使用property\_getAttributes函数发现属性的名称和@encode类型字符串。关于编码类型字符串的细节，参阅[Type Encodings](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW1)；该字符串的细节，参阅[Property Type String](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW6)和[Property Attribute Description Examples](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW5)。

```
const char *property_getAttributes(objc_property_t property)
```

把这些放在一起，你可以使用下面的代码打印所有类关联的属性的列表：

```
id LenderClass = objc_getClass("Lender");
unsigned int outCount, i;
objc_property_t *properties = class_copyPropertyList(LenderClass, &outCount);
for (i = 0; i < outCount; i++) {
    objc_property_t property = properties[i];
    fprintf(stdout, "%s %s\n", property_getName(property), property_getAttributes(property));
}
```

## 属性类型字符串（Property Type String）

## 属性关键字描述示例（Property Attribute Description Examples）



