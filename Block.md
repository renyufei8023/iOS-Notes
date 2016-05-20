#Block
###Block概述
Block它是C语言级别和运行时方面的一个特征。Block封装了一段代码逻辑，也用{}括起，和标准C语言中的函数/函数指针很相似，此外就是blokc能够对定义环境中的变量可以引用到。这一点和其它各种语言中所说的“闭包”是非常类似的概念。在iOS中，block有很多应用场景，比如对代码封装作为参数传递。这在使用dispatch并发（Operation中也有BlockOperation）和completion异步回调等处都广泛应用。

- Block是苹果官方特别推荐使用的数据类型，使用场景比较广泛
 - 动画
 - 多线程
 - 集合遍历
 - 网络请求回调
- Block的作用
 - 用来保存某一段代码，可以在恰当时候再去出来调用
 - 功能类似于函数和方法
 
  
1. Block的基本使用

```
声明：(返回类型)(^声明的block名称)(参数列表);

实现：^(参数列表){代码块}

返回值类型 (^block变量名)(参数列表) = ^(形参列表) {
}；
```
赋值的例子：

```objc
int sum(int value1, int value2)
{
    return value1 + value2;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int (*sumP)(int, int);
        sumP = sum;
        NSLog(@"sum = %i",sumP(10,20));
        int (^sumBlock) (int value1, int value2) = ^(int value1,int value2) {
            return value2 + value1;
        };
        
        NSLog(@"sum----%i",sumBlock(10,30));
    }
    return 0;
}
```
其实Block和C语言中的指向函数的指针差不多
其实我们完全可以定义C语言的函数，如下所示

```objc
int sum(int value1, int value2)
{
    return value1 + value2;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int (*sumP)(int, int);
        sumP = sum;
        NSLog(@"sum = %i",sumP(10,20));
        
    }
    return 0;
}
```

有时为了方便，用typedef给出定义，在苹果的官方文档里，建议出现多次的block使用typedef定义。

而block的调用，则十分简单：block名称(参数列表);

2. 使用中的注意点
- block中可以定义和外界同名的变量，并且如果在block中定义了和外界同名的变量，在block中访问的是block内部的变量
- 默认情况下，在block内部不能改变外面变量的值
 - 因为block中变量和外面的变量并不是同一个变量，可以通过打打印变量的地址看出来
 - 如果block中访问到了外界的变量，block会将外界变量拷贝一份到堆内存中
 - 因为block中使用的外面变量的值是拷贝过来的即值拷贝，所以在调用之前修改外界变量的值，不会影响到block中拷贝的值
 - 如果想在block中修改外界变量的值，必须在外界变量前面加上`__block`
 - 如果在block中修改了外界变量的值，会影响到外界变量的值，
 
 我们可以把.m反编译成c++文件，看看其内部是怎么实现`__block`这个关键词可以修改外部变量的
 
```cpp
int sum(int value1, int value2)
{
    return value1 + value2;
}


struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int flags=0) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static int __main_block_func_0(struct __main_block_impl_0 *__cself, int value1, int value2) {

            return value2 + value1;
        }

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};
int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        int (*sumP)(int, int);
        sumP = sum;
        NSLog((NSString *)&__NSConstantStringImpl__var_folders_8b_skbhgyln78718lwjds4398k80000gn_T_main_185299_mi_0,sumP(10,20));
        int (*sumBlock) (int value1, int value2) = ((int (*)(int, int))&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA));

        NSLog((NSString *)&__NSConstantStringImpl__var_folders_8b_skbhgyln78718lwjds4398k80000gn_T_main_185299_mi_1,((int (*)(__block_impl *, int, int))((__block_impl *)sumBlock)->FuncPtr)((__block_impl *)sumBlock, 10, 30));
    }



    return 0;
}
static struct IMAGE_INFO { unsigned version; unsigned flag; } _OBJC_IMAGE_INFO = { 0, 2 };

```

加上__block

```cpp
int sum(int value1, int value2)
{
    return value1 + value2;
}

struct __Block_byref_a_0 {
  void *__isa;
__Block_byref_a_0 *__forwarding;
 int __flags;
 int __size;
 int a;
};

struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  __Block_byref_a_0 *a; // by ref
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, __Block_byref_a_0 *_a, int flags=0) : a(_a->__forwarding) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static int __main_block_func_0(struct __main_block_impl_0 *__cself, int value1, int value2) {
  __Block_byref_a_0 *a = __cself->a; // bound by ref

            (a->__forwarding->a) = 20;
            return value2 + value1;
        }
static void __main_block_copy_0(struct __main_block_impl_0*dst, struct __main_block_impl_0*src) {_Block_object_assign((void*)&dst->a, (void*)src->a, 8/*BLOCK_FIELD_IS_BYREF*/);}

static void __main_block_dispose_0(struct __main_block_impl_0*src) {_Block_object_dispose((void*)src->a, 8/*BLOCK_FIELD_IS_BYREF*/);}

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
  void (*copy)(struct __main_block_impl_0*, struct __main_block_impl_0*);
  void (*dispose)(struct __main_block_impl_0*);
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0), __main_block_copy_0, __main_block_dispose_0};
int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        int (*sumP)(int, int);
        sumP = sum;
        NSLog((NSString *)&__NSConstantStringImpl__var_folders_8b_skbhgyln78718lwjds4398k80000gn_T_main_9f1f9e_mi_0,sumP(10,20));
        __attribute__((__blocks__(byref))) __Block_byref_a_0 a = {(void*)0,(__Block_byref_a_0 *)&a, 0, sizeof(__Block_byref_a_0), 10};
        int (*sumBlock) (int value1, int value2) = ((int (*)(int, int))&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, (__Block_byref_a_0 *)&a, 570425344));

        NSLog((NSString *)&__NSConstantStringImpl__var_folders_8b_skbhgyln78718lwjds4398k80000gn_T_main_9f1f9e_mi_1,((int (*)(__block_impl *, int, int))((__block_impl *)sumBlock)->FuncPtr)((__block_impl *)sumBlock, 10, 30));
    }



    return 0;
}
static struct IMAGE_INFO { unsigned version; unsigned flag; } _OBJC_IMAGE_INFO = { 0, 2 };

```
很明显加上了__block之后传入的是&这样的，是把地址传到block内部去了，所以可以修改外部变量了

- block是存储在堆中还是栈中呢
 - 默认情况下block是存储在栈中的，如果对block进行一个copy操作，block会转移到堆中
 - 如果block在栈中 ，block中访问了外界的对象，那么不会对对象进行retain操作
 - 但是如果block在堆中，block中访问了外面的对象，那么会对外界的对象进行一次retain操作
 - 如果block中访问了外界的对象，一定要给对象加上__block，只要加上__block，哪怕block在堆中，也不会对外界的对象进行retain
 - 想把block弄到堆中可以使用`Clock_copy()`
 
###有如下一些注意点：
对定义环境的变量使用。默认是以const的方式使用，这有点像函数的const参数传递，如过需要block内修改可变，则使用__block，这样做存储就实现了共享，包括块中的递归应用和定义环境上下文中的多个block使用。block通常定义在栈帧当中，而当所处的栈帧被销毁的时候，block以及引用到的__block变量将会依然有效。

引用类型问题。block中的引用默认都是强引用，必要的时候需要使用__weak，同delegate使用的注意一样，避免循环引用。此外，苹果文档中还给出了对instanceVariable和对localVariable引用不同的例子，注意体会下。

copy。在类属性中，要使用copy。此外，对block进行copy要使用Block_copy()/Block_release()。

苹果文档中几种需要避免使用的方式。《Blocks Programming Topics》中Using Blocks中的例子，实际上就是要注意block定义的位置与其上下文的关系。

有关Block的地址/引用。注意这篇文章中最后例子中的问题：http://www.cnblogs.com/kesalin/archive/2013/04/30/ios_block.html我的理解是Block的地址发生了变化，最终的问题是对Block引用地址释放时的野指针错误。



