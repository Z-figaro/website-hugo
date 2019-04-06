---
title: "Gitbook使用总结"
date: 2018-12-19T15:36:44+08:00

featuredImg: ""
tags: 
  - gitbook
---
本文选自《Markdown 实用指南》 作者：毕小烦

GitBook 是一个基于 Node.js 开发的命令行工具，使用它可以很方便的管理电子书，GitBook 是目前最流行的开源书籍写作方案。

使用 GitBook 可以让创作者专注写作，自由的挥洒，不必太多的关心内容排版、发布和版本管理等问题。

除了通过 GitBook 命令行管理电子书外，还可以在线（gitbook.com）或者使用桌面编辑器 Gitbook Editor 来编写和管理电子书。

对于新手来说，我推荐使用 Gitbook Editor 来编写和管理电子书，因为它集成了 GitBook 命令、Markdown 编器和 Git 的功能，内容通过 Git 可与 gitbook.com / Gitlab / GitHub 同步，方便版本管理和团队协作。

可对于程序员或熟悉 Git 和 Markdown 的人，我更推荐使用 GitBook 命令行 + Typora + SourceTree 来编写和管理电子书。使用专业工具做专业的事，比 Gitbook Editor 高效很多。

GitBook 是开源的，地址：https://github.com/GitbookIO/gitbook

<!--more-->

GitBook 的功能：

支持 Markdown 或 AsciiDoc 语法
可导出静态站点或电子书（PDF、epub、mobi）
支持多语言
可设置封面
支持变量、模板和模板继承
有丰富的插件
GitBook 的工具组合：

Node.js：为了使用 npm 安装 GitBook，一定要先安装 Node.js。
GitBook 命令：基于 Node.js 开发的命令行工具，用于创建和管理电子书。
Markdown：GitBook 使用 Markdown 来写作，作者再也不用担心排版了。
Git：GitBook 使用 Git 管理写作内容，方便多人协作和版本管理。
Github/Gitlab：跟代码一样，写作的内容可以托管在 Github 或 Gitlab 中。
gitbook.com：用于在线编写、发布和托管电子书的网站。
GitBook 的使用场景：

搭建公司内部的文档平台，用于公司内部的资料共享。
发表开源的电子书，用于在互联网上共享自己的知识，普惠大众。
GitBook 环境配置
开始之前我们需要先安装：

nodejs
gitbook
安装 Node.js
因为 GitBook 是使用 Node.js 开发的，需要通过 Node.js 包管理工具 NPM 安装，所以在开始之前要先把 Nodejs 安装好。

Node.js 下载地址：https://nodejs.org/zh-cn/

NPM（node package manager），通常称为 node 包管理器。使用 NPM 可以对 node 包进行安装、卸载、更新、查看、搜索、发布等操作。

安装完 Node.js，NPM 就可以直接用了。

1.安装 GitBook
使用 NPM 安装 GitBook 的命令行工具：

`$ sudo npm install gitbook-cli -g`

2.安装完成后查看 GitBook 的版本：


```
$ gitbook --version
CLI version: 2.3.0
GitBook version: 3.2.2
```
3.更新 GitBook命令：


```
$ npm update gitbook-cli -g
```
4.卸载 GitBook 命令：


```
$ sudo npm uninstall gitbook-cli -g
```
注意：

本例中使用的是全局安装 GitBook 命令，还有一种安装方式是本地安装，两种方式有什么不同呢？

本地安装：安装包会被下载到当前所在目录，因此只能在当前目录下使用。
全局安装：安装包会被下载到到特定的系统目录下，安装包能够在所有目录下使用。
例如，查看 macOS 下全局安装被安装到了哪里？


```
$ which gitbook
/usr/local/bin/gitbook
```
GitBook 被安装到了 /usr/local/bin/ 目录下，因此可以全局使用。



快速开始 GitBook
初始化 GitBook
# 创建 mygitbook 文件夹，并切换到这个文件夹下面

```
~$ mkdir mygitbook && cd mygitbook
```

# 初始化 gitbook 工作目录，创建必要的文件

```
~$ gitbook init
warn: no summary file in this book
info: create README.md
info: create SUMMARY.md
info: initialization is finished
```
然后会初始化 GitBook 目录，创建两个 md 格式的文件 README.md 和 SUMMARY.md

README.md - 项目的介绍都写在这个文件里。
SUMMARY.md - GitBook 的目录结构在这里配置。

定义目录结构
两种方法

在 SUMMARY.md 文件中定义目录结构有两种方法。

方法 ➊. 先定义好目录结构，通过 gitbook init 自动生成目录结构对应的文件夹和 Markdown 文件。

方法 ➋. 先创建好文件夹和 Markdown 文件再来编辑目录结构。

SUMMARY.md

SUMMARY.md 的目录结构长什么样？ 看下面：

# Summary

* [项目简介](README.md)
* [快速开始](docs/快速开始.md)
 * [环境搭建](docs/环境搭建.md)
 * [简单使用](docs/简单使用.md)
* [学入学习](docs/深入学习) 
这个目录建好以后在根目录下执行命令：


```
~$ gitbook init
```
那些没有的目录和文件都会被创建：

info: create docs/快速开始.md
info: create docs/环境搭建.md
info: create docs/简单使用.md
info: create docs/深入学习.md
info: create SUMMARY.md
info: initialization is finished
注意： gitbook init 只支持生成两级目录

启动服务
在根目录执行命令：


```
~$ gitbook serve
```
结果：


```

```
```Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 7 plugins are installed
info: loading plugin "livereload"... OK
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "sharing"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 5 pages
info: found 0 asset files
info: >> generation finished with success in 1.9s !

Starting server ...
Serving book on http://localhost:4000 # 注意浏览地址
```
执行 gitbook serve命令后，会先编译书籍gitbook build，如果没有问题会打开一个 Web 服务器，默认监听 4000 端口。如果编译有问题，会抛出错误信息。

查看效果
用浏览器打开 http://localhost:4000/ 或 http://127.0.0.1:4000/ 查看显示书籍的效果。


如果想发给别人看，把 localhost 换成自己电脑的 IP 地址。

Tips:

Linux 上查看 IP 地址的命令是： ifconfig

Windows 上查看 IP 地址的命令是：ipconfig

本文选自《Markdown 实用指南》 作者：毕小烦

作者：毕小烦
链接：https://www.jianshu.com/p/430f4569d776


