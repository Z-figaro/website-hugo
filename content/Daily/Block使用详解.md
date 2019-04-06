---
title: "Block使用详解"
date: 2019-04-02T11:26:34+08:00

toc: false
images:
tags: 
  - iOS
---


# Block本质

通过[LLVM的编译器-rt子项目存储库](https://llvm.org/svn/llvm-project/compiler-rt/trunk/lib/BlocksRuntime/)中找到runtime下的Block源码
```
struct Block_layout {
    void *isa;
    int flags;
    int reserved;
    void (*invoke)(void *, ...);
    struct Block_descriptor *descriptor;
    /* Imported variables. */
};
```

我们实现一个block，就是实现了这个结构体，isa指向block具体的类，invoke函数对应oc中代码的具体实现，所以block是oc的对象。
因为block是一个对象，所以它可以作为一个参数来传递，也可以作为一种属性来操作，它本身也像一个闭包函数。所以block基本上就变成了一把瑞士军刀，它可以极大的简化代码，并且结构紧凑。你每天一定在不停的使用block。那么让我们来彻底掌握block的原理，使用方法，和注意事项。

# Block的使用：

block可以简化代码，并且实现链式，函数式编程。因为它可以返回，传递它本身。
如果有一套操作链，那么最好用block封装好一系列操作步骤。

## block 的声明

block的书写规则比较难写：

作为局部变量

> returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};

作为属性（property）
> @property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);

定义方法时，作为方法参数

>  -(void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;

调用方法时，作为参数传递
> [someObject someMethodThatTakesABlock:^returnType (parameters) {...}];

作为类型别名 （typedef），增加代码可读性
> typedef returnType (^TypeName)(parameterTypes);
> TypeName blockName = ^returnType(parameters) {...};


这里我们强烈建议按照最后一种方式typedef方式书写，可以极大的提高代码可读性。

这里按顺序展示block的使用：
声明：

```
void(^blockName)(NSString * str,BOOL success);
 //省略参数的写法：void(^blockName1)(NSString *,BOOL);
```

block的调用：

```
blockName(@"bwrgrets",YES);
```

block的实现：


```
blockName = ^(NSString * str,BOOL success){
        //block的实现部分，block体
        //声明和赋值时，block体中的代码都不会执行
        NSLog(@"执行blokc体中的代码");
    };
```
## __block 修饰局部变量
说截获自动变量之前我们先看以下代码


```
int tmp = 2;
int (^blk) (int) = ^(int count){
    return count+tmp;
};
tmp = 3;
int result = blk(2);
NSLog(@"%d",result);
```

以上代码会打印出多少呢？5还是4？？ 正确答案是4
为什么是4呢？其实就是因为Block截获自动变量的原因

```
struct __ViewController__viewDidLoad_block_impl_0 {
  struct __block_impl impl;
  struct __ViewController__viewDidLoad_block_desc_0* Desc;
  int tmp;
  __ViewController__viewDidLoad_block_impl_0(void *fp, struct __ViewController__viewDidLoad_block_desc_0 *desc, int _tmp, int flags=0) : tmp(_tmp) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static int __ViewController__viewDidLoad_block_func_0(struct __ViewController__viewDidLoad_block_impl_0 *__cself, int count) {
  int tmp = __cself->tmp; // bound by copy

        return count+tmp;
    }

static struct __ViewController__viewDidLoad_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __ViewController__viewDidLoad_block_desc_0_DATA = { 0, sizeof(struct __ViewController__viewDidLoad_block_impl_0)};

static void _I_ViewController_viewDidLoad(ViewController * self, SEL _cmd) {
    ((void (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){(id)self, (id)class_getSuperclass(objc_getClass("ViewController"))}, sel_registerName("viewDidLoad"));
    int tmp = 2;
    int (*blk) (int) = ((int (*)(int))&__ViewController__viewDidLoad_block_impl_0((void *)__ViewController__viewDidLoad_block_func_0, &__ViewController__viewDidLoad_block_desc_0_DATA, tmp));
    tmp = 3;
    int result = ((int (*)(__block_impl *, int))((__block_impl *)blk)->FuncPtr)((__block_impl *)blk, 2);
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_6n_mdf6rn0d5f5cw6r1h2620h3w0000gn_T_ViewController_b80e84_mi_0,result);
}`__ViewController__viewDidLoad_block_impl_0`

```
通过以上代码可以看出,当我们在给Block类型变量赋值的时候，tmp变量同时被传入，并且被保存到了__ViewController__viewDidLoad_block_impl_0的struct中。这时候其实就是截获了自动变量，由于已经在struct类中保存了一份，即使后边更改，也不会影响Block截获的值。


```
int (*blk) (int) = ((int (*)(int))&__ViewController__viewDidLoad_block_impl_0((void *)__ViewController__viewDidLoad_block_func_0, &__ViewController__viewDidLoad_block_desc_0_DATA, tmp));
```

为什么要对局部变量进行截获呢？而全局变量和静态变量不需要截获，并且修改的的时候也不需要加__block呢？

主要原因就是**变量的生命周期**。局部变量在代码块执行结束之后就会被释放，但是Block不一定在此时释放。所以就会出现变量超过生命周期的现象，此时对局部变量进行截获，即使局部变量被释放，但是Block同样还是可以正常使用的。因为全局变量和静态变量的释放时间肯定不会在Block之前，所以不必对他们进行截获。

> 全局变量和静态变量存储在全局数据区；局部变量存储在栈中。
不知道大家有没有想过一个问题，为什么需要__block呢？如果没有__block难道就修改不了变量了吗？

我们先看一下加了__block编译器给我们做了啥


```
struct __Block_byref_tmpBlock_0 {
  void *__isa;
__Block_byref_tmpBlock_0 *__forwarding;
 int __flags;
 int __size;
 int tmpBlock;
};

struct __ViewController__viewDidLoad_block_impl_0 {
  struct __block_impl impl;
  struct __ViewController__viewDidLoad_block_desc_0* Desc;
  __Block_byref_tmpBlock_0 *tmpBlock; // by ref
  __ViewController__viewDidLoad_block_impl_0(void *fp, struct __ViewController__viewDidLoad_block_desc_0 *desc, __Block_byref_tmpBlock_0 *_tmpBlock, int flags=0) : tmpBlock(_tmpBlock->__forwarding) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static int __ViewController__viewDidLoad_block_func_0(struct __ViewController__viewDidLoad_block_impl_0 *__cself, int count) {
  __Block_byref_tmpBlock_0 *tmpBlock = __cself->tmpBlock; // bound by ref

        (tmpBlock->__forwarding->tmpBlock) = 100;
        return count+(tmpBlock->__forwarding->tmpBlock);
    }
static void __ViewController__viewDidLoad_block_copy_0(struct __ViewController__viewDidLoad_block_impl_0*dst, struct __ViewController__viewDidLoad_block_impl_0*src) {_Block_object_assign((void*)&dst->tmpBlock, (void*)src->tmpBlock, 8/*BLOCK_FIELD_IS_BYREF*/);}

static void __ViewController__viewDidLoad_block_dispose_0(struct __ViewController__viewDidLoad_block_impl_0*src) {_Block_object_dispose((void*)src->tmpBlock, 8/*BLOCK_FIELD_IS_BYREF*/);}

static struct __ViewController__viewDidLoad_block_desc_0 {
  size_t reserved;
  size_t Block_size;
  void (*copy)(struct __ViewController__viewDidLoad_block_impl_0*, struct __ViewController__viewDidLoad_block_impl_0*);
  void (*dispose)(struct __ViewController__viewDidLoad_block_impl_0*);
} __ViewController__viewDidLoad_block_desc_0_DATA = { 0, sizeof(struct __ViewController__viewDidLoad_block_impl_0), __ViewController__viewDidLoad_block_copy_0, __ViewController__viewDidLoad_block_dispose_0};

static void _I_ViewController_viewDidLoad(ViewController * self, SEL _cmd) {
    ((void (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){(id)self, (id)class_getSuperclass(objc_getClass("ViewController"))}, sel_registerName("viewDidLoad"));
    __attribute__((__blocks__(byref))) __Block_byref_tmpBlock_0 tmpBlock = {(void*)0,(__Block_byref_tmpBlock_0 *)&tmpBlock, 0, sizeof(__Block_byref_tmpBlock_0), 2};
    int (*blk) (int) = ((int (*)(int))&__ViewController__viewDidLoad_block_impl_0((void *)__ViewController__viewDidLoad_block_func_0, &__ViewController__viewDidLoad_block_desc_0_DATA, (__Block_byref_tmpBlock_0 *)&tmpBlock, 570425344));
    int result = ((int (*)(__block_impl *, int))((__block_impl *)blk)->FuncPtr)((__block_impl *)blk, 2);
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_6n_mdf6rn0d5f5cw6r1h2620h3w0000gn_T_ViewController_a1c583_mi_0,result);
}

```

这时候大家可以与前边没有加__block的代码进行比较，两者的差别在哪里。 其实大家应该很容易发现加了__block之后，变量形成了一个struct，这个struct中保存了变量的值，同时还有一个__forwarding。这个__forwarding其实就是为什么需要__block的关键。

Block从栈复制到堆的时候，__block变量也会受到影响。如下:

| __block变量的配置存储域 | Block从栈到堆时的影响 |
| --- | --- |
| 栈 | 从栈复制到堆并被Block持有 |
| 堆 | 被Block持有 |


ARC下，Block如果是栈的话，默认会copy到堆上。此时所使用的__block变量同时也会从栈被复制到堆上如下图

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190403145037.png?token=ANanysxB8RtkCal3Zzl19dfrzf0Q5Mx9ks5cpFgKwA%3D%3D)
 

那如果Block在堆上了，我们在Block中修改了变量，怎么让栈上的变量也同时能正确访问呢？这其实就是__forwarding功劳了。

__block变量初始化的时候__forwarding是指向本身自己的。当__block变量从栈复制到堆上的时候，此时会将__forwarding的值替换为复制到目标堆上的__block变量用结构体实例的地址。如下图：

 
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190403144858.png?token=ANanyrYm0e7R6XKH9fyEwvL8f4-ihE5tks5cpFecwA%3D%3D)

 

通过此功能，无论是在Block语法中、Block语法外使用__block变量，还是__block变量配置在栈上或堆上，都可以顺利地访问一个__block变量。 到这里大家应该明白了"__block加了之后，是把变量的地址传入Block"的说法是很片面的吧啦
## block为什么用copy修饰

> 栈是吃了吐 堆是吃了拉

默认情况下，block会存档在栈中(栈是吃了吐)，所以block会在函数调用结束被销毁，在调用会报空指针异常，如果用copy修饰的话，可以使其保存在堆区(堆是吃了拉) ，它的生命周期会随着对象的销毁而结束的。只要对象不销毁，我们就可以调用在堆中的block。
有时候，你会发现你用了strong也不会有任何问题，其实就是符合某些条件而已。官方建议copy修饰。

block有三种：

1. 全局block
2. 堆block
3. 栈block

这里先不展开讲，以后慢慢补充。


## BlocksKit 大神示范block可以做什么

这里就有非常非常多，可以学习的地方了。当你以为搞懂了block的时候，你可以仔细看看这个三方库，这里会教你做人。

这里有篇[文章](https://blog.csdn.net/ZeroOnet/article/details/77159789)对BlocksKit有一定的分析，可以先看看。之后我也会慢慢做源码分析和做一些实际的项目。

