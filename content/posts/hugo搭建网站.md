---
title: "Hugo搭建网站"
date: 2018-12-18T17:14:28+08:00
featuredImg: ""
tags: 
  - hugo
---

  我用了很多博客框架，最后还是选择了go语言开发的hugo。不管速度还是简单程度都完全不一样。
  <!-- more -->
  
## 搭建流程：
以下是使用流程和心得，如果时间过久应该以[官方文档](https://gohugo.io/documentation/)为准。
### 安装：
    使用brew安装hugo
    

> 没有brew的话：
   
```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
> 安装hugo

```
$ brew new Hugo
```

### hugo使用：

在你想存放网站的地方使用：

```
$ hugo new site mysite
```
进入路径

```
$ cd mysite
```
在该目录下面你可以看到这些文件

```
#blog
#├── archetypes
#│   └── default.md
#├── config.toml
#├── content
#├── data
#├── layouts
#├── static
#└── themes
```
其中最关键的是config.toml,这是网站的配置文件。

* archetypes：包括内容类型，在创建新内容时自动生成内容的配置
* content：包括网站内容，全部使用markdown格式
* layouts：包括了网站的模版，决定内容如何呈现
* static：包括了css, js, fonts, media等，决定网站的外观
* themes: 网站主题文件

你可以下载自己喜欢的主题，或者使用默认的。

主题使用submodule来git clone 是最方便的；也是官方建议的。

### 安装主题：

在[官方地址](https://themes.gohugo.io/)下载主题有两种clone方式。

可以直接cd到theme主题文件下

```
git clone https://github.com/olOwOlo/hugo-theme-even themes/even
```
也可以添加到git的submodule中，如果做travis自动部署，或者对主题做了修改。那么这是首选的方式。


```
git submodule add https://github.com/olOwOlo/hugo-theme-even.git themes/even
```

如果修改了主题，这个要根据每个主题文件不同的设置。有的需要在主题目录下重新build

```
cd themes/even && npm i && npm start
```

### 页面设置和发布文章

如果你想根据主题设置网站page：

```
hugo new xxxx.md
```
如果你想生成新文章：
```
hugo new posts/my-first-post.md
```
### 评论功能：

可以参考[这篇文章](https://zh4ui.net/post/2017-04-20-hugo-with-disqus/)

### github page结合使用

可以参考[这里](https://segmentfault.com/a/1190000012975914)，了解基本部署和自动部署。

如果需要手动部署：

1. 修改完文章之后修改draft状态为false，或者直接删除该行
2. 命令行使用hugo 生成新的public文件
3. push 新的public文件到github
4. 等待一会就能看到了







