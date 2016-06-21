[class new]和[[class alloc]init]源码

```objc
+ new 
{ 
    id newObject = (*_alloc)((Class)self, 0); 
    Class metaClass = self->isa; 
    if (class_getVersion(metaClass) > 1) 
    return [newObject init]; 
    else 
    return newObject; 
} 

//而 alloc/init 像这样： 
+ alloc 
{ 
    return (*_zoneAlloc)((Class)self, 0, malloc_default_zone());  
} 
- init 
{ 
    return self; 
}
```
alloc从应用程序的虚拟地址空间上为该对象分配内存，并且把新对象的引用计数器加1，将对象的成员变量初始化为0，init会做一些初始化操作，并且为对象的实例变量赋予合理有用的值。
alloc返回一个有效的为初始化的实例对象，self和alloc返回的指针都可以在其所有的方法作用域内进行访问。

但是对于 super，它只是一个"编译器指示符",告诉编译器在父类中搜索方法的实现。

优先调用[super init] 是为了使超类完成它们自己的初始化工作。


if (self = [super init])的作用:

这里是担心父类初始化失败，如果初始化一个对象失败，就会返回nil，当返回nil的时候self = [super init]测试的主体就不会再继续执行。如果不这样做，你可能会操作一个不可用的对象，它的行为是不可预测的，最终可能会导致你的程序崩溃。


