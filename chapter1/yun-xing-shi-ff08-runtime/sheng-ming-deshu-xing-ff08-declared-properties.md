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

你可以使用property\_getAttributes函数发现属性的名称、@encode类型字符串和属性的其他属性。

该字符串以T开头，后跟@encode类型和逗号，并以V结尾，后面跟着支持实例变量的名称。 在这些之间，属性由以下描述符指定，用逗号分隔：

声明的属性类型编码

| Code | Meaning |
| :--- | :--- |
| R | The property is read-only \(readonly\). |
| C | The property is a copy of the value last assigned \(copy\). |
| & | The property is a reference to the value last assigned \(retain\). |
| N | The property is non-atomic \(nonatomic\). |
| G&lt;name&gt; | The property defines a custom getter selector name. The name follows the G \(for example, GcustomGetter,\). |
| S&lt;name&gt; | The property defines a custom setter selector name. The name follows the S \(for example, ScustomSetter:,\). |
| D | The property is dynamic \(@dynamic\). |
| W | The property is a weak reference \(\_\_weak\). |
| P | The property is eligible for garbage collection. |
| t&lt;encoding&gt; | Specifies the type using old-style encoding. |

例子，请参阅[Property Attribute Description Examples](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW5)。

## 属性关键字描述示例（Property Attribute Description Examples）

鉴于这些定义：

```
enum FooManChu { FOO, MAN, CHU };
struct YorkshireTeaStruct { int pot; char lady; };
typedef struct YorkshireTeaStruct YorkshireTeaStructType;
union MoneyUnion { float alone; double down; };
```

下表显示了示例属性声明和property\_getAttributes返回的相应字符串：

| Property | Property description |
| :--- | :--- |
| @property char charDefault; | Tc,VcharDefault |
| @property double doubleDefault; | Td,VdoubleDefault |
| @property enum FooManChu enumDefault; | Ti,VenumDefault |
| @property float floatDefault; | Tf,VfloatDefault |
| @property int intDefault; | Ti,VintDefault |
| @property long longDefault; | Tl,VlongDefault |
| @property short shortDefault; | Ts,VshortDefault |
| @property signed signedDefault; | Ti,VsignedDefault |
| @property struct YorkshireTeaStruct structDefault; | T{YorkshireTeaStruct="pot"i"lady"c},VstructDefault |
| @property YorkshireTeaStructType typedefDefault; | T{YorkshireTeaStruct="pot"i"lady"c},VtypedefDefault |
| @property union MoneyUnion unionDefault; | T\(MoneyUnion="alone"f"down"d\),VunionDefault |
| @property unsigned unsignedDefault; | TI,VunsignedDefault |
| @property int \(\*functionPointerDefault\)\(char \*\); | T^?,VfunctionPointerDefault |
| @property id idDefault;Note: the compiler warns: "no 'assign', 'retain', or 'copy' attribute is specified - 'assign' is assumed" | T@,VidDefault |
| @property int \*intPointer; | T^i,VintPointer |
| @property void \*voidPointerDefault; | T^v,VvoidPointerDefault |
| @property int intSynthEquals;In the implementation block:@synthesize intSynthEquals=\_intSynthEquals; | Ti,V\_intSynthEquals |
| @property\(getter=intGetFoo, setter=intSetFoo:\) int intSetterGetter; | Ti,GintGetFoo,SintSetFoo:,VintSetterGetter |
| @property\(readonly\) int intReadonly; | Ti,R,VintReadonly |
| @property\(getter=isIntReadOnlyGetter, readonly\) int intReadonlyGetter; | Ti,R,GisIntReadOnlyGetter |
|  |  |



