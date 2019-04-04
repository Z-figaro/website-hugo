---
title: " Attribute 实现系统代码提示"
date: 2019-04-03T16:55:07+08:00

toc: false
images:
tags: 
  - attribute
  - iOS
---


[资料](http://releases.llvm.org/3.8.0/tools/clang/docs/AttributeReference.html#opencl-address-spaces)放在最前面，方便同学查询。
[官方文档](https://gcc.gnu.org/onlinedocs/gcc-4.0.0/gcc/Function-Attributes.html)
[matt大神](https://nshipster.com/__attribute__/)

attribute是GNU C特色之一,在iOS用的比较广泛.系统中有许多地方使用到. attribute可以设置函数属性（Function Attribute ）、变量属性（Variable Attribute ）和类型属性（Type Attribute)等.

## 函数属性(Function Attribute)

* noreturn
* noinline
* always_inline
* pure
* const
* nothrow
* sentinel
* format
* format_arg
* no_instrument_function
* section
* constructor
* destructor
* used
* unused
* deprecated
* weak
* malloc
* alias
* warn_unused_result
* nonnull


## 类型属性(Type Attributes)

* aligned
* packed
* transparent_union,
* unused,
* deprecated
* may_alias

## 变量属性(Variable Attribute)

* aligned
* packed

## Clang特有的

* availability
* overloadable


以上是我抄录的，并不只有这些。
# 书写格式

书写格式：attribute后面会紧跟两对原括弧，括弧里面是相应的attribute参数


```
__attribute__((xxx))
```

## 使用场景和技巧

系统使用了宏定义封装了很多，建议遇到或者需要使用的特意记录一下。毕竟真的很多很多，你也不太常用的。
其中我整理了一下比较常用的一些参数，转录了很多不同作者总结的，站在巨人的肩膀上；边走边抄。

### 更新了，之前的废弃


```
//可以自定义描述信息
__attribute__((deprecated("已过期!")))

//系统的宏定义
DEPRECATED_ATTRIBUTE 
```
我们经常看到三方或者系统中，有的方法废弃了。比如alertview，那么我们就应该这样写。


```
//标记这个属性已过期
@property (nonatomic, strong) NSString *name __attribute__((deprecated("属性已过期")));

//标记方法已过期
- (void) testOld __attribute__((deprecated("方法已过期, 请使用 test2"))) {  
}

- (void) testNew {
}

-(void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];

    //编译器警告，'testOld' is deprecated: 已过期, 请使用 testNew
    [self testOld];

    //编译器警告，提示 "'name' 已过期..."
    NSLog(@"%@", self.name);
}
```

### 更新了，不能用


```
//可以自定义描述信息
__attribute__((unavailable("已经废弃,请使用 xxxx")))


//系统宏定义
NS_UNAVAILABLE;
UNAVAILABLE_ATTRIBUTE;
```
使用场景：

* 自定义了类的初始化方法，不希望外界使用初始化初始化，并且给出正确的提示。比如单例不能使用init方法，应该用自定义的初始化方法。
* 比希望继续使用某个属性，并且给出正确的提示。


```
@interface ViewController : UIViewController
@property (nonatomic, strong) NSString *name __attribute__((unavailable("这个属性已经废弃")));

#pragma mark - 初始化
-(instancetype)init __attribute__((unavailable("这个方法已经废弃,请使用 initWithName:")));

-(instancetype)initWithName:(NSString *)Name;
@end

//测试方法

- (void) test {
    //编译不通过，提示 "'init' 已经废弃了..."
    ViewController *vc = [[ViewController alloc] init];

    //编译不通过，提示 "'name' 已经废弃了..."
    NSLog(@"%@", vc.name);
}
```

### block中提示self可能引起循环引用


```
__attribute__((ns_consumes_self))

//系统宏定义
NS_REPLACES_RECEIVER

//系统的封装
#if __has_feature(attribute_ns_consumes_self)
#define NS_REPLACES_RECEIVER __attribute__((ns_consumes_self)) NS_RETURNS_RETAINED
#else
#define NS_REPLACES_RECEIVER
#endif
```

这是我在一些组件功能的时候，发现别人会提示在block中需要注意的。


```
-(NSString *)stringManager:(stringBlock) resultString NS_REPLACES_RECEIVER;

它只能在方法中使用
self.subString = ^NSString *(NSString *string) {
        //这里就是弹警告
        [self testOld];
        NSLog(@"string -- %@",string);
        return [string substringFromIndex:1];
    };
```

### 在哪些平台或者版本可用


```
__attribute__((availability(ios,introduced=6.0,deprecated=8.0,obsoleted=10.0,message="ios10.0中废弃")));

ios：iOS平台
introduced：开始使用的版本
deprecated：开始弃用的版本
obsoleted：禁止使用的版本

只能在方法中使用
```

### 变量的作用域结束时，调用指定的函数



```
__attribute__((cleanup(作用域结束调用的函数名)))
```


```
void intCleanup(int *value){
    NSLog(@"cleanup:%d",*value);
}

void stringCleanup(__strong NSString **value){
    NSLog(@"cleanup:%@",*value);
}

void rectCleanup(CGRect *value){
    CGRect temp = *value;
    NSString *str = NSStringFromCGRect(temp);
    NSLog(@"cleanup:%@",str);
}

void appDelegateCleanup(__strong AppDelegate **value){
    NSLog(@"cleanup:%@",*value);
}

int main(int argc, char * argv[]) {
    //第一个作用域
    {
        int a __attribute__((cleanup(intCleanup))) = 10;
    }
    //第二个作用域
    {
        NSString *string __attribute__((cleanup(stringCleanup))) = @"张三";
        CGRect rect __attribute__((cleanup(rectCleanup))) = {0,0,1,1};
    }
    //第三个作用域
    {
        AppDelegate *delegate __attribute__((cleanup(appDelegateCleanup))) = [[AppDelegate alloc] init];
    }
}


//控制台输出
1.xxx[6549:886516] cleanup:10
2.xxx[6549:886516] cleanup:{{0, 0}, {1, 1}}
3.xxx[6549:886516] cleanup:张三 //用一个作用域，这一个cleanup 是最先加的
4.xxx[6780:893605] cleanup:<AppDelegate: 0x6040000337a0>
5.xxx[6780:893605] AppDelegate dealloc //dealloc 在 cleanup之后
```

别人的总结：

```
1.一个作用域内有若干个cleanup的变量，他们的调用顺序是先入后出的栈式顺序
2.调用时机：cleanup比dealloc早
3.作用域的结束包括:大括号结束、return、goto、break、exception等
```
所以我感觉可以控制作用域和代码执行顺序：


```
//指向block的指针,觉得不好理解可以用typeof
void blockCleanUp(void(^*block)()){
    (*block)();
}

 void (^block)(void) __attribute__((cleanup(blockCleanUp))) = ^{
        NSLog(@"finish block");
    };

```
这个好处就是,不用等到block最后才写某些代码,我们可以把它放在block的任意位置,防止忘记.

参考：[黑魔法](http://blog.sunnyxx.com/2014/09/15/objc-attribute-cleanup/#rd)

### 一个类不能有子类

使用与类，该类不能有子类
```
__attribute__((objc_subclassing_restricted))
```


```
#import <Foundation/Foundation.h>

__attribute__((objc_subclassing_restricted))
@interface KDClangTest : NSObject
@end


#import "KDClangTest.h"

//这里编译出错，提示“Cannot subclass a class that was declared with the 'objc_subclassing_restricted' attribute”
@interface KDClangSonTest : KDClangTest
@end

```
### 子类要重写某个方法，必须调用super



```
//通用写法
__attribute__((objc_requires_super))

//系统宏定义，其实和上面是一样的
NS_REQUIRES_SUPER

``` 


```
#import <Foundation/Foundation.h>

@interface KDClangTest : NSObject

- (void)instanceMethod1 __attribute__((objc_requires_super));
@end


#import "KDClangTest.h"

@interface KDClangSonTest : KDClangTest
@end
@implementation KDClangSonTest


-(void)instanceMethod1 {
    NSLog(@"I am son");
    //这里编译器会出现警告： Method possibly missing a [super instanceMethod1] call
}
```


### 在函数某个参数不能为空



```
__attribute__((nonnull (x)))
```

```
- (int)addNum1:(int *)num1 num2:(int *)num2  __attribute__((nonnull (1,2))){//1,2表示第一个和第二个参数不能为空
    return  *num1 + *num2;
}

- (NSString *)getHost:(NSURL *)url __attribute__((nonnull (1))){//第一个参数不能为空
    return url.host;
}
```


### 类或协议的名字在编译时指定成另一个


```
__attribute__((objc_runtime_name("xxx")))
```


```
在编译时指定成另一个.示例如下:
__attribute__((objc_runtime_name("NSObject")))
@interface SGObject :NSObject

@end

 //调用
 NSLog(@"%@",[SGObject class]);
 //输出
 2016-07-22 11:18:00.934 Study[14355:5516261] NSObject

```


很有很多[例子](https://medium.com/@liushuaikobe/%E4%B8%8Eclang%E5%85%B1%E8%88%9E-attribute-8b6bc839958c)
[很多很多](https://www.jianshu.com/p/29eb7b5c8b2d)


