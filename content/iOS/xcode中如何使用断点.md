---
title: "Xcode中如何使用断点"
date: 2019-03-21T13:34:06+08:00

toc: false
images:
tags: 
  - iOS
---

# 断点调试

断点使用是我们开发提高效率，解决问题的不二法门；能够清楚的使用断点调试技巧，是基本技能。

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190321152623.png?token=ANanygXSAT9Zqbl4xpwd_xtaZQBoj6Hdks5ckzziwA%3D%3D)


* edit breakpoint 编辑断点
* reveal in breakpoint navigator 断点导航器

这就是我们的两个武器。

## Edit Breakpoint 

是编辑断点信息，点击之后是：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190321152717.png?token=ANanyhxVEzBNmR0bNakNse7ofsMgd6bGks5ckz0XwA%3D%3D)


* condition

    顾名思义，这是断点生效的条件。
    不能够识别：
    1. 断点之外作用域的变量，函数
    2. 预处理的宏定义
* ignore
    
    忽略次数，该断点生效几次之后才执行。
* action
    
    重点：断点生效之后。执行什么操作。提供了6种方案。
    1. [applescript](https://blog.csdn.net/yang3wei/article/details/7964226) 执行脚本
    2. Capture GPU Frame 捕获GPU当前所绘制的帧。该功能是辅助图形调试的
    3. Debugger Command  让断点执行LLDB调试命令，例如 po xxx
    4. Log Message 生成信息，和nslog很像
    5. Shell Command 执行一个命令文件和参数列表 
    6. Sound  发出制定的声音
    
* options
    直接执行不停顿，返回action的结果。


接下来说明了概念就来实际操作：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190321152745.png?token=ANanyiNEVogkBvALzhsx2axsclHm2apVks5ckz00wA%3D%3D)

中间就是脚本语言写的内容，所以你可以写一套专门用来描述你这个断点要展示的信息的专用弹框。

其中右侧显示的
> @expression@ = LLDB表达式
> %B = 断点的名称
> %H = 遇到该断点的次数

使用好这些信息将会很酷，"Your breakpoint %B has been hit %H times"
试试把这短话放到图示哪里。

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-03-21%20%E4%B8%8B%E5%8D%883.48.10.png?token=ANanym9lEcifSqOfjbAnGI4Bg_R_NbQ0ks5ck0J2wA%3D%3D)

如此，你可以得到一些总结性的调试信息。


然后是最常用的 debugger command，这里使用的语句和在调试台使用的一模一样，如果不清楚可以看[这里](https://southpeak.github.io/2015/01/25/tool-lldb/)。**强烈建议阅读**

常用的如：

print xxx 显示全部信息
po  xxx   显示变量的值
exp a = xxx  改变 a的值，同时在代码和调试中生效
image使用：

```
2015-01-25 14:12:01.007 test[18122:76474] *** Terminating app due to uncaught exception 'NSRangeException', reason: '*** -[__NSArrayI objectAtIndex:]: index 2 beyond bounds [0 .. 1]'
*** First throw call stack:
(
	0   CoreFoundation                      0x00007fff8e06f66c __exceptionPreprocess + 172
	1   libobjc.A.dylib                     0x00007fff886ad76e objc_exception_throw + 43
	2   CoreFoundation                      0x00007fff8df487de -[__NSArrayI objectAtIndex:] + 190
	3   test                                0x0000000100000de0 main + 384
	4   libdyld.dylib                       0x00007fff8f1b65c9 start + 1
)
libc++abi.dylib: terminating with uncaught exception of type NSException

```                    
使用 image lookup定位错误 test具体的代码行数：
image lookup --address

```

(lldb) image lookup --address 0x0000000100000de0
      Address: test[0x0000000100000de0] (test.__TEXT.__text + 384)
      Summary: test`main + 384 at main.m:23
```
image lookup --type 查看具体类型


```
(lldb) image lookup --type NSURL
Best match found in /Users/**/Library/Developer/Xcode/DerivedData/test-byjqwkhxixddxudlnvqhrfughkra/Build/Products/Debug/test:
id = {0x100000157}, name = "NSURL", byte-size = 40, decl = NSURL.h:17, clang_type = "@interface NSURL : NSObject{
    NSString * _urlString;
    NSURL * _baseURL;
    void * _clients;
    void * _reserved;
}
@property ( readonly,getter = absoluteString,setter = <null selector>,nonatomic ) NSString * absoluteString;
@property ( readonly,getter = relativeString,setter = <null selector>,nonatomic ) NSString * relativeString;
@property ( readonly,getter = baseURL,setter = <null selector>,nonatomic ) NSURL * baseURL;
@property ( readonly,getter = absoluteURL,setter = <null selector>,nonatomic ) NSURL * absoluteURL;
@property ( readonly,getter = scheme,setter = <null selector>,nonatomic ) NSString * scheme;
@property ( readonly,getter = resourceSpecifier,setter = <null selector>,nonatomic ) NSString * resourceSpecifier;
@property ( readonly,getter = host,setter = <null selector>,nonatomic ) NSString * host;
@property ( readonly,getter = port,setter = <null selector>,nonatomic ) NSNumber * port;
@property ( readonly,getter = user,setter = <null selector>,nonatomic ) NSString * user;
@property ( readonly,getter = password,setter = <null selector>,nonatomic ) NSString * password;
@property ( readonly,getter = path,setter = <null selector>,nonatomic ) NSString * path;
@property ( readonly,getter = fragment,setter = <null selector>,nonatomic ) NSString * fragment;
@property ( readonly,getter = parameterString,setter = <null selector>,nonatomic ) NSString * parameterString;
@property ( readonly,getter = query,setter = <null selector>,nonatomic ) NSString * query;
@property ( readonly,getter = relativePath,setter = <null selector>,nonatomic ) NSString * relativePath;
@property ( readonly,getter = fileSystemRepresentation,setter = <null selector> ) const char * fileSystemRepresentation;
@property ( readonly,getter = isFileURL,setter = <null selector>,readwrite ) BOOL fileURL;
@property ( readonly,getter = standardizedURL,setter = <null selector>,nonatomic ) NSURL * standardizedURL;
@property ( readonly,getter = filePathURL,setter = <null selector>,nonatomic ) NSURL * filePathURL;
@end"
```

## 断点类型

### 异常断点

异常断点是代码出现问题导致编译器抛出异常时触发的断点。它在断点导航器中设置。点击＋号，选择Exception Breakpoint选项。
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190321161608.png?token=ANanysOjcF-bA53iW8LXYC7l4o6viNlAks5ck0iJwA%3D%3D)

**Exception**
选择响应Objective－C对象抛出的异常，也可以选择响应C++对象抛出的异常。

**Break**
选择断点所接收的异常，是接收“Throw”语句抛出的异常还是Catch语句的
**Action**
和上面出现的一样，都是断点生效后的操作。
**Options**
自动执行action，不中断。

### 符号断点

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/20190321162159.png?token=ANanyjTfR8eUG_3EpIkHX-mHMnGbL-E-ks5ck0nowA%3D%3D)

他可以中断某个方法的调用，可谓是异常强大，在断点导航器界面，点击＋号，选择Add Symbolic Breakpoint选项。

**Symbol**
他用来设置当前断点作用域所能识别的方法，这里面既可以是自定义的方法，也可以是系统的API方法。（注意必须表明是类方法还是成员方法）

**Module**
用来限制满足符号的方法，编译器将只会在断点满足这个模组的符号的时候才回暂停。


设定一个场景，你想知道对任何controller调用viewdidload方法。那么我们设定断点-[uiviewcontroller viewdidload]
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-03-21%20%E4%B8%8B%E5%8D%884.27.09.png?token=ANanygjOO8xPaVVOynZ69RXCrF3-80JVks5ck0vcwA%3D%3D)

然后系统会显示所有使用该方法的地方加上断点。

这样如果换成你自己写的方法，那么你可以很轻松的找到使用了该方法的地方；都会停顿。如此排查一些公共组件，或者陌生的方法会非常轻松。

## OpenGL ES Error Breakpoint

这个是调试OpenGL ES的断点调试，个人没用到过。

## constraint error

这个是约束错误断点调试，凡是出现约束错误就会执行这个。


## 总结：

各种xcode的调试技巧


