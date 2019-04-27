---
title: "APP国际化指南"
date: 2018-12-19T15:39:51+08:00
draft: false
featuredImg: ""
tags: 
  - iOS
  - Python
---

# iOS国际化完全指南

看完这篇文章你可以解决国际化的绝大部分问题，并且本人重写了前人的脚本；可以实现代码，xib，storyboard的自动国际化。自动新增，删除文字，项目中的所有内容，不需要再手动维护，新项目简单国际化，老项目轻松国际化。[项目地址](https://github.com/Z-figaro/ZPPLanguage)

我试图用XCodeEditor来实现一个mac应用，让我们的国际化完全不用在项目中操作，单个语言文件没问题，但是因为多个国际化语言的文件引用关系始终不能正确设置，所以没有成功，如果有mac开发的大佬或者对project.pbxproj 这个文件工程核心文件结构有了解的同学；可以联系我一起实现这个[项目]()

如果有不清楚的地方，欢迎联系我补充。

## 国际化的基本姿势

一个项目要国际化，首先要在project 的 build settings 中添加需要的文件：

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-28%20%E4%B8%8A%E5%8D%881.44.28.png?token=ADLKPSTG5DGAT2GFFAHPNVS4YSKVA)

这里是选择整个项目要支持的语言，点+号选择你需要的就好了。

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/xib%E5%9B%BD%E9%99%85%E5%8C%962019-04-28%20%E4%B8%8A%E5%8D%881.56.05.png?token=ADLKPSS6V3CNPTVNTCWY5FK4YSMCA)


### Xib和stroyboard国际化

如果是新项目，我强烈建议你劲量多的使用 xib 和 stroyboard 来构建项目，因为如果你遇到了要支持阿拉伯地区的语言，他们的习惯是从右到左的，也就是系统的RTL（rignt to left）。整个界面都需要镜像翻转，那时候系统提供的方法只支持xib 和stroyboard 。你如果用的纯代码写死，那么会麻烦很多。

xib和SB要国际化，其实很简单；只需要在相应的文件中选择
点击Localization，选择相应的语言，系统就会自动修改项目的实际文件结构：

![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-28%20%E4%B8%8A%E5%8D%881.58.46.png?token=ADLKPSVRD5MJPTVSH6PNUFK4YSMK6)

会出现以下的项目样子：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-28%20%E4%B8%8A%E5%8D%882.02.06.png?token=ADLKPSRBRPXIZWBFSNAA2QS4YSM2G)

这里要注意下，系统自动生成的文件中会出现已经在xib或者SB 中使用的字符；但是只会生成在你点击Localization那一个时刻已经有了的字符，如果你持续开发。又添加删除了控件，那么不会自动生成。只有取消该部分国际化，然后再把这种语言打钩，选择上之后才会生成新的文件，并且会覆盖掉你之前已经写好的国际化文件。本脚本已经解决这个问题，不再需要手动维护。解决原理是：


```
ibtool --generate-stringsfile MyNib.strings MyNib.nib
```
ibtool这个小工具，系统自带。可以自动把xib或者sb 生成一个.stings文件。

### 代码国际化

代码部分的国际化，只有一个办法就是使用系统的国际化宏：

```
NSLocalizedString("这里是需要国际化的字符",comment: "这里是注释")

#define NSLocalizedString(key, comment) \
	    [NSBundle.mainBundle localizedStringForKey:(key) value:@"" table:nil]

...
/* Method for retrieving localized strings. */
- (NSString *)localizedStringForKey:(NSString *)key value:(nullable NSString *)value table:(nullable NSString *)tableName NS_FORMAT_ARGUMENT(1);


#define NSLocalizedStringFromTable(key, tbl, comment) \
	    [NSBundle.mainBundle localizedStringForKey:(key) value:@"" table:(tbl)]
#define NSLocalizedStringFromTableInBundle(key, tbl, bundle, comment) \
	    [bundle localizedStringForKey:(key) value:@"" table:(tbl)]

```
NSLocalizedStringFromTable(key, tbl, comment) 这种方式比较少用，因为这个tbl就是tbl.strings的名字，在代码部分使用了宏之后。你还需要生成资源文件.strings:
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-28%20%E4%B8%8A%E5%8D%882.26.26.png?token=ADLKPSVIMCXP7B5EV5O44JS4YSPVU)

你生成这个文件的时候，输入的文件名如果是 _Localizable_ 那么你就使用第一个宏，如果你输入了别的名字，那么你就应该使用 NSLocalizedStringFromTable(key, tbl, comment)。其中的tbl就是你输入的文件名称。

我的python脚本只支持第一种方式的宏，只要你实现了这个宏。那么我的脚本就支持全自动导入，自动添加，删除。建议在comment部分写上文件名，如果你有了上千个需要翻译的文字，那么文件很大的时候。方便你定位具体的字符。
我的实现原理是借助了：

```
genstrings -o .m en.lproj
```
genstrings 系统自带，可以把一个文件使用了宏的字符，生成对应的文件到 en.lproj 目录中去。

我的脚本已经处理了细节问题，不再需要手动维护。国际化只需要填空就好了。

### 图片，影视频国际化

这部分国际化很简单:
1. 代码部分
    和在代码的文字部分一样，只要取好key，然后在不同的语言中写好相应的名字就可以。
2. xib和SB部分
    在项目中专门建立一个文件目录放置图片。然后和xib，sb一样。在右侧文件资源监控器中点Localization，然后在项目实际目录中修改就好了。
    
图片，视频，音频没有区别。

### 系统权限国际化---InfoPlist

系统权限部分生成多语言文件部分和其他的没有区别，你只需要在生成的文件中：

```
NSLocationAlwaysAndWhenInUseUsageDescription = “使用定位的描述”
```

查找对应的权限名称，只需要选择showRaw就会出现：
![](https://raw.githubusercontent.com/Z-figaro/picBed/master/image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-04-28%20%E4%B8%8A%E5%8D%883.03.40.png?token=ADLKPSQELAGJBTIJPFRF3V24YST56)

### 系统资源国际化

比如进入调取相册页面，通讯录页面,还有复制粘贴的系统提示等，这部分是跟着
CFBundleDevelopmentRegion 这个权限，系统开发语言走的。这个是系统默认语言。当什么都没有的时候，它就会是英文。如果你修改了。系统资源页面会根据你的修改生效。这里很有意思，有两种选择方式：一种是选择国家，一种是选择具体的语言范围。这是两个概念！！如果你要修改默认开发语言，选择国际。如果你要修改系统资源页面，你要选择语言范围。比如：zh_CN

### 服务器端国际化

这部分没有什么特别的好办法，纯粹是两端协商实现。一般就两种：
1.后端完全实现相应的语言提示返回给前端。
2.后端不管语言，返回一套自己规定的提示语言编号集合。前端根据这个编号提示相应的内容。


## 应用内语言选择

其实在iOS中国际化，就是切换不同的语言资源，显示不同的文件信息。然后系统用了一个Localizable的系统宏，来维护key/value的关系。系统中每一种语言对应一种bundle资源文件。当你在切换不同语言的时候，就去找对应的语言包中的资源。所以我们可以利用runtime动态加载不同的bundle来实现语言的切换。在demo中有具体的使用例子。

这个部分一般只有两种实现方式：
1.微信的方式：切换bundle。
2.微博的方式：发送语言切换通知。

这部分讨论的文章已经非常多，并且很成熟了；我就不过多赘述。

## 疑难问题解决

1.阿拉伯地区支持RTL
2.textView系统不支持
3.AttributedString富文本国际化

太晚了，之后慢慢更新。






