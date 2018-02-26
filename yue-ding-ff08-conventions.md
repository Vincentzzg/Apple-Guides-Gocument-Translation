# 约定（Conventions）

当你写自己的类的时候，你和其他可能参与到你的项目的其他开发者都应该遵守跟Cocoa和Cocoa Touch使用的同样的协议，以使代码更易读，并保持代码库的一致。

另外，许多Objective-C和框架特性要求你遵守严格的明明协议，目的是各种机制能正常运行。例如，访问器方法命名，必须遵守约定以使用如[Key-Value Coding]() \(KVC\) 或 [Key-Value Observing]() \(KVO\)技术。

## 一些名字必须应用内唯一

### 类名必须在整个应用内唯一

Objective-C类必须命名唯一不仅在你项目中编写的代码中，而且在你可能包含的任何框架或捆绑包中唯一地命名。

为了保持类名的唯一，协议是在所有类上使用前缀。你应该已经看到Cocoa和Cocoa Touch类名总是以NS或UI开头。像这样的两个单词的前缀被苹果保留用在框架类上。随着你对Cocoa和Cocoa Touch的了解更多，你将见到各种各样跟特定框架相关的其他前缀：

| Prefix | Framework |
| :--- | :--- |
| NS | Foundation \(OS X and iOS\) and Application Kit \(OS X\) |
| UI | UIKit \(iOS\) |
| AB | Address Book |
| CA | Core Animation |
| CI | Core Image |

你自己的类应该使用三个字母前缀。前缀可能涉及您的公司名称和您的应用名称的结合，甚至可能与应用程序中的特定组件有关。

你应该也使用一个能使类的代表清晰的名词命名你的类，像这些Cocoa和Cocoa Touch的例子：

|  | NSWindow | CAAnimation | NSWindowController | NSManagedObjectContext |
| :--- | :--- | :--- | :--- | :--- |






### 方法名应该在一个类内具有表现性和唯一性

### 局部变量必须在同一范围内唯一

## 一些方法名必须遵循约定

### 访问器方法名必须遵守约定

### 对象创建方法名必须遵守约定

## 



