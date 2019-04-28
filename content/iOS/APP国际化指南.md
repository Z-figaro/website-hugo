---
title: "APP国际化指南"
date: 2018-12-19T15:39:51+08:00
draft: true
featuredImg: ""
tags: 
  - iOS
---

    

# APP国际化完全指南

本文有的部分只适合iOS9之后的项目，不适用的部分；我会单独指出来。请注意！

国际化在早期，是一件特别费时费力的事情；尽管官方给了一些非常好的案例；但是知识点琐碎。并且如果项目是半途或者已经开发完，中途加入国际化的。那么工作就会变得非常棘手。经过了这些年的发展，国际化方案有了一些进步，本人也做了一套工具。站在前人的肩膀上，更进一步。

## 高效国际化

在官方文档中，已经有了国际化方法。但是实际上，有非常多的细节和坑，让我们没有能够实现高效这一原则。ZPPGlobal 的目的就是让我们从大量的枯燥工作中解脱出来，只需要提供相应的国际化文案，就可以直接简单高效的完成项目的国际化。我的目标是新项目简单完成，老项目轻松完成。


## 国际化的原理

在iOS中国际化就是用一套资源文件来管理项目中的文本和图片，音视频等，使它在需要的语言下，显示我们设定的资料。每一个控件都会生成独一无二的objectID，然后用key/value的方式，找到独一无二的key，根据key显示不同的value。本质上就是这么简单。

### 新项目 Vs 老项目

新项目相比老项目会简单非常多，只要我们使用以下的方法就可以实现。老项目的解决方法也会在各个部分具体说明。

#### APP语言修改方式

项目要随系统语言切换，非常简单。完成了需要国际化的资源文件，用户修改了系统语言，在APP重启之后就会自动转换。不过，我们一般需要的是应用内切换，其中又有两种方式。
1.微信一样的，设定了语言之后；APP重新加载，选定某一个页面重新打开app。
2.微博这样的，对整个项目的所有页面发通知，根据自己的设定可以定制化的控制某些页面的显示。
第一种方式，相对简单一些；第二种方式需要一开始就对项目有整体的规划。

如图所示：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-18%20%E4%B8%8B%E5%8D%886.12.11.png?token=ADLKPSWOJ4URI4TDBSXPPN24XBHJS)

在工程中，选择project———— info ———— locallizations 中可以看到当前使用的语言资源，点击+号，就可以添加相应的国际化语言资源。需要显示几种语言，就添加几种。
系统默认是使用英文的，表示开发语言是英文。要修改默认开发语言，在info.plist中修改：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-18%20%E4%B8%8B%E5%8D%886.21.09.png?token=ADLKPSVYFGHB3EAOQZ6CPIC4XBILO)

在代码中 使用系统的宏：NSLocalizedString 对每一个字符串进行修饰，然后在对应的语言资源文件中，填写相应的key值对应的value。这样选择了不同的语言，就可以显示不同的文字。是不是很简单？不过这和我们的要求还离得很远。想象一下，你项目中，有几千个不同的文本，其中有重复的，也有不同的。那你要自己手动写几千个不同的k/v?

系统的宏：
```
#define NSLocalizedString(key, comment) \
	    [NSBundle.mainBundle localizedStringForKey:(key) value:@"" table:nil]
#define NSLocalizedStringFromTable(key, tbl, comment) \
	    [NSBundle.mainBundle localizedStringForKey:(key) value:@"" table:(tbl)]
#define NSLocalizedStringFromTableInBundle(key, tbl, bundle, comment) \
	    [bundle localizedStringForKey:(key) value:@"" table:(tbl)]
#define NSLocalizedStringWithDefaultValue(key, tbl, bundle, val, comment) \
	    [bundle localizedStringForKey:(key) value:(val) table:(tbl)]
	    
```

#### 文本国际化
#### 图片国际化
#### xib/SB 国际化
#### 三方国际化
#### APP图标国际化
#### 启动图国际化
#### 应用名称国际化
#### 权限提示国际化
#### APP调取系统资源页面国际化
#### 服务端数据内容国际化


## 使用方法

## 总结

