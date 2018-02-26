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

| NSWindow | CAAnimation | NSWindowController | NSManagedObjectContext |
| :--- | :--- | :--- | :--- |


### 方法名应该在一个类内具有表现性和唯一性

方法名不需要前缀，且应该以小写字母开头；多个单词时也使用驼峰命名，像NSString类里的这些例子:

| length | characterAtIndex: | lengthOfBytesUsingEncoding: |
| :--- | :--- | :--- |


如果方法有一个或多个参数，方法名应该指明每个参数：

| substringFromIndex: | writeToURL:atomically:encoding:error: | enumerateSubstringsInRange:options:usingBlock: |
| :--- | :--- | :--- |


方法名的第一部分应该指明主要的意图或调用方法的结果。例如，如果一个方法返回一个值，第一个单词通常指明将返回什么，像length，character...和substring...这些前面展示的方法。如果你需要指明一些重要的返回值，可以使用多个单词，像NSString类中的mutableCopy，capitalizedString或lastPathComponent方法。如果一个方法执行一个动作，比如写入磁盘或者枚举内容，第一个单词应该指明动作，像write...和enumerate...方法展示的一样。

如果一个方法包含一个用来在产生错误时设置的_error_指针参数，它应该是最后一个参数。如果一个方法有块参数，块参数应该作为最有一个参数，目的是当指定一个内联块时任何方法调用都尽可能易读。同样的原因，尽可能避免使用多个块参数的方法。

清晰而简洁的方法名也很重要。清晰并不一定意味着详尽，但简洁并不一定会清晰，所以最好把握好分寸：

| stringAfterFindingAndReplacingAllOccurrencesOfThisString:withThisString: | 太冗长了 |
| :--- | :--- |
| strReplacingStr:str: | 太简洁 |
| stringByReplacingOccurrencesOfString:withString: | 刚刚好 |



### 局部变量必须在同一范围内唯一

## 一些方法名必须遵循约定

### 访问器方法名必须遵守约定

### 对象创建方法名必须遵守约定

## 



