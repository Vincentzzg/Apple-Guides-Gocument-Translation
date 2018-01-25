# 数据存储

## 数据结构

`数据结构`：数据存在的形式。  
数据结构有哪些？

## 存储方式

存储方式分两种：内存和闪存  
`内存存储`：是临时的，运行时有效，效率高。  
`闪存`：持久化存储，但产生I/O消耗，效率相对低。![](/assets/存储方案示意图.png)
_存储方案示意图_

四种数据存储方式：
* NSUserDefaults：用于存储配置信息。
* SQLite：用于存储查询需求较多的信息。
* CoreData：用于规划应用中的对象
* 使用基本对象类型定制的存储方案



### NSUserDefaults

`NSUserDefaults`被设计用来存储设备和用户的配置信息。
`NSUserDefaults`以字典的形式把配置信息组织起来，支持基础类型数据的存储。可以通过- (NSDictionary<NSString *, id> *)dictionaryRepresentation方法取得对应的字典打印出存储的数据。
![](/assets/2.png)
[NSUserDefaults standardUserDefaults]中的数据

`NSUserDefaults`会将所有数据都放到内存里，因此操作速度很快。开发者自定义的配置项会以plist格式的文件归档在应用目录的/Library/Preferences/[App_Bundle_Identifier].plist文件。再次初始化获得实例对象后，框架会把用户自定义的配置信息和系统的配置信息合并并得到完整数据。


### SQLite

SQLite是遵守ACID的`关系数据库管理系统`。
iOS的SDK内置了SQLite的库。
SQLite每次写入数据都会产生IO消耗，把数据归档到相应的文件。
一般来说应用中`格式化的文本数据`可以存放在数据库中，尤其是类似聊天记录、TimeLine等这些具有条件查询和排序需求的数据。

每一个数据库的句柄都会在内内存中被分配一段缓存，用于提高查询效率。另一方面由于查询缓存，当产生大量句柄或数据量过大时，会出现缓存过大，造成内存浪费。

针对SQLite的封装：把相对复杂的SQLite命令封装成对象和方法。


#### 数据库相关概念

`数据库`（英语：Database）：是以一定的方式存储在一起、能予多个用户共享、具有尽可能小的冗余度、与应用程序彼此独立的数据集合。用户可以对其中的数据进行新增、截取、更新、删除等操作。  
`关系数据库`：创建在`关系模型`基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。  
`SQL`（英语：Structured Query Language）：一种基于`关系数据库`的标准数据查询语言，这种语言执行对关系数据库中数据的检索和操作。  
`关系模型`：是采用二维表格结构表达实体类型以及实体间联系的数据模型。  
`数据库管理系统`（英语：Database Management System，缩写：DBMS）：是一种针对对象数据库，为管理数据库二设计的大型电脑软件管理系统。

`索引`：是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。不可重复，不能为空。  
`主键`（primary key）：是表中的一个或多个字段，它的值用于唯一地标识表中的某一条记录。主键不能为空。如“INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL”
`事务`（英语：transaction）：是用户定义的一个数据库操作序列。这些操作要么全做，要么全不做，是一个不可分区的工作单位。

#### SQLite七层架构
![](/assets/arch2.gif)

整个系统核心分为三个子系统：“前段（SQL编译器）”、“中层（虚拟机）”、“后端（存取、缓冲、操作系统抽象）”

### CoreData
开发中没使用过CoreData

CoreData是一个模型层的技术。

官方：一个持久化的，对象图和生命周期的自动化`管理方案`。它可以把整个应用中的对象建模并进行自动化的管理。
严格上来说CoreData是一个管理方案，它的持久化可以通过SQLite、XML或二进制文件储存。


正准备去学习CoreData的时候，看到了唐巧的这篇博客（[我为什么不喜欢 Core Data](http://blog.devtang.com/2016/08/04/i-do-not-like-core-data/)）
。哈哈，之前也看了一些相关的文章，基本都看不懂，这下好了，直接不用学了。
总结下就是：CoreData相关概念难以理解，学习成本太高。使用起来也很复杂，使用成本也高。然后还有很多坑，维护成本高。总之就是成本高，收益少。


CoreData使用介绍：[iOS CoreData数据库之创建详解](https://www.jianshu.com/p/880dd63c5f5e)

### 使用基本数据对象定制的缓存方案
![](/assets/5.png)
Kache架构图


参考文章：  
[对比iOS中的四种数据存储](http://www.infoq.com/cn/articles/data-storage-in-ios)


### 文件存储
沙盒机制

默认情况下，沙盒有三个文件夹：Document、Library、tmp，Library包含Caches和Preference
`Docments`：苹果建议将应用产生的文件数据存在这个目录下，iTunes备份和回复的时候会包括此目录。
`Library`：存储程序的默认设置和其他状态信息。
`Library/Caches`：存放缓存文件，保存应用的持久化数据，用于应用升级或应用关闭后的数据保存，不会被iTunes同步，所以为了减少同步的时间，可以将一些比较大的文件而又不需要备份的文件放在这个目录下。
`Library/Preferences`：保存应用的所有偏好设置，iOS的Setting应用会在该目录下寻找应用的设置信息，iTunes会自动备份该目录。
`tmp`：保存应用运行时所需的临时数据，在应用关闭后，该目录下的数据会被删除，也可能系统在程序不运行的时候清除。

`文件对接器`
NSFileHandle：主要负责对文件内容的读取和写入操作。支持从文件指定位置追加内容


#### 文件相关操作
参考文章：[文件操作（NSFileManager）](http://blog.csdn.net/xyz_lmn/article/details/8968213)


#### 复杂对象的读写（I/O）操作
复杂对象：Foundation中不存在的数据类，比如自定义的Person类，这种自定义的数据类无法直接通过writeToFile写入文件

复杂对象写入文件的过程：复杂对象->序列化（归档）->NSData->写入文件
从文件中读取复杂对象的过程：读取文件->NSData->反序列化（反归档）->复杂对象

1、首先复杂对象类要遵守<NSCoding>协议
2、实现两个协议方法：
* -(void)encodeWithCoder:(NSCoder *)aCoder; 序列化
* -(id)initWithCoder:(NSCoder *)aDecoder; 反序列化

归档工具类：NSKeyedArchiver
反归档工具类：NSKeyedUnarchiver


#### XML属性列表（plist）归档
属性列表是一种XML格式的文件，拓展名plist
















