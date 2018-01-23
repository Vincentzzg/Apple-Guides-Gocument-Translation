# 属性字段

**@property**属性关键字

@property的常用属性关键字有nonatomic、atomic、readonly、writeonly、readwrite、assign、retain、copy、strong、weak、unsafe\_unretained、nonnull、nullable、null\_resettable，看着挺多的，但是经常用的也就那几个。

* atomic:
   默认关键字，也就是说如果什么都不写，默认就是这个。表示该属性是线程同步的。一般用不到，会影响性能。

* nonatomic:
   非线程同步，基本都是用这个。

* readwrite:
   默认关键字，表示可读可写。
* readonly:
   只能读\(get\)，不能写\(set\)，当你希望属性不能被外界直接修改，但是可以访问时使用。 
* writeonly: 
  只能写\(set\)，不能读\(get\)。一般用不到。

* assign:
   默认关键字。非对象类型一般使用此关键字。
* retain:
   对象的引用计数+1。ARC下已经不再使用此关键字，用strong代替。
* copy:
   拷贝一个新的对象，新对象的引用计数+1，原对象不变。
* strong:
   能够维持对象的生命。

  




