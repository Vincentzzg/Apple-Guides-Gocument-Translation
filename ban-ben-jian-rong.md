# 版本兼容

## 编译设置

**Base SDK**

指的是当前编译所用的SDK版本。

**Deployment Target**

它控制着运行系统需要的最低系统版本。

##  {#iOS版本宏}

## 系统预设宏定义 {#iOS版本宏}

1、**系统版本的宏定义**

随便拷贝一个宏到代码中，command + 鼠标左键寻找代码来源，可以看到usr/include/Availability.h头文件中有下面这些系统版本相关的宏定义：

```
#define __IPHONE_2_0      20000
#define __IPHONE_2_1      20100
#define __IPHONE_2_2      20200
#define __IPHONE_3_0      30000
#define __IPHONE_3_1      30100
#define __IPHONE_3_2      30200
#define __IPHONE_4_0      40000
#define __IPHONE_4_1      40100
#define __IPHONE_4_2      40200
#define __IPHONE_4_3      40300
#define __IPHONE_5_0      50000
#define __IPHONE_5_1      50100
#define __IPHONE_6_0      60000
#define __IPHONE_6_1      60100
#define __IPHONE_7_0      70000
#define __IPHONE_7_1      70100
#define __IPHONE_8_0      80000
#define __IPHONE_8_1      80100
#define __IPHONE_8_2      80200
#define __IPHONE_8_3      80300
#define __IPHONE_8_4      80400
#define __IPHONE_9_0      90000
#define __IPHONE_9_1      90100
#define __IPHONE_9_2      90200
#define __IPHONE_9_3      90300
#define __IPHONE_10_0    100000
#define __IPHONE_10_1    100100
#define __IPHONE_10_2    100200
#define __IPHONE_10_3    100300
#define __IPHONE_11_0    110000
#define __IPHONE_11_1    110100
#define __IPHONE_11_2    110200
/* __IPHONE_NA is not defined to a value but is uses as a token by macros to indicate that the API is unavailable */
```

另外还有mac、TVOS、WATCHOS的系统版本宏

**2、\_\_IPHONE\_OS\_VERSION\_MIN\_REQUIRED**

当前项目支持运行的最低版本，相当于在Xcode中配置的Deployment Target

**3、\_\_IPHONE\_OS\_VERSION\_MAX\_ALLOWED**

当前开发环境的系统SDK版本，相当于Xcode中配置的Base SDK

## 

## 针对编译所用的不同SDK版本使用不同API

```
#if __IPHONE_OS_VERSION_MAX_ALLOWED > __IPHONE_4_3    
    #if __IPHONE_OS_VERSION_MAX_ALLOWED > __IPHONE_7_0
        // iOS SDK 7.0 以后版本的处理    
    #else        
        // iOS SDK 5.0 ~ 6.1版本的处理    
    #endif
#else
    // iOS SDK 4.3 之前版本的处理
#endif
```

## 判定系统版本，根据运行时的版本号运行代码



## 对于类可以检测其是否存在

如果要使用的新特性是个类的话，可以使用NSClassFromString来将字符串转为类，如果Runtime没有这个类存在，则返回nil

```
if(NSClassFromString(@"CLGeocoder")) {//iOS5中新类，替代MKReverseGeocoder
做位置解析
    //CLGeocoder存在，可以使用
} else{
    //只能使用MKReverseGeocoder
}
```



