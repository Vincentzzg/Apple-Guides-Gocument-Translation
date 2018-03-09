# 类型编码（Type Encodings）

为了协助运行时系统，编译器为每个方法编码返回和参数类型到一个字符串并且将字符串和方法选择器关联起来。它使用的编码方案在其他上下文也很有用，因此可以通过@encode\(\)编译器指令公开获得。当给出一个类型说明时，@encode\(\)返回一个字符串编码该类型。类型可以是基础类型比如int，指针，一个标记的结构或联合，或一个类名--实际上可以用作C sizeof\(\)运算符参数的任何类型。

```
char *buf1 = @encode(int **);
char *buf2 = @encode(struct key);
char *buf3 = @encode(Rectangle);
```

下面的表格列出了类型代码。注意它们中的许多与用于存档或分发目时编码对象的代码重叠。然而，这里列出的有些代码你不能用作编写编码器，并且你想用作编写编码器的代码有些不是通过@encode\(\)产生的。（有关编码用于归档或分发的编码对象的更多信息，请参阅Foundation Framework参考中的NSCoder类规范。）

| Code | Meaning |
| :--- | :--- |
| c | A char |
| i | An int |
| s | A short |
| l | A long. 1 is treated as a 32-bit quantity on 64-bit programs. |
| q | A long long |
| C | An unsigned char |
| I | An unsigned int |
| S | An unsigned short |
| L | An unsigned long |
| Q | An unsigned long long |
| f | A float |
| d | A double |
| B | A C++ bool or a C99 \_Bool |
| v | A void |
| \* | A character string \(char \*\) |
| @ | An object \(whether statically typed or typed id\) |
| \# | A class object \(Class\) |
| : | A method selector \(SEL\) |
| \[array type\] | An array |
| {name=type...} | A structure |
| \_b\_num | A bit field of num bits |
| ^type | A pointer to type |
| ? | An unknown type \(among other things, this code is used for fucntion pointers\) |



