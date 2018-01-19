# 应用安全开发

## 一、网络安全

见网络部分下的网络安全



## 二、本地文件和数据安全

### 本地数据安全

加密存储、保存到keychain中，保证本地数据安全

## 三、源代码安全 {#源代码安全}

代码编译后是二进制文件，通过 file, class-dump, theos, otool 等工具可以查看到编译后的二进制文件。

另外还有IDA：

[IDA](https://www.hex-rays.com/products/ida/)是一个收费的反汇编工具，对于Objective-C代码来说，它可以反汇编到方便阅读的程度。通过阅读源码，黑客可以很方便的分析出应用的通讯协议和数据加密方式。

应对措施：使用宏来混淆类名、关键逻辑用纯C实现。





参考文章：

[iOS应用安全开发概述](http://blog.devtang.com/2014/05/08/ios-security-dev-overview/)



