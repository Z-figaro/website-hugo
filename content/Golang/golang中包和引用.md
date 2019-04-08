---
title: "Golang中包和引用"
date: 2019-03-06T16:07:44+08:00

tags: 
  - golang
---

    在golang中，初次使用很容易对包和引用产生疑问，特此记录下来。
    
因为golang对文件引用的严格管理，但是对于项目结构又给与了很大的自由度；所以我自学起来第一次建立项目，安排项目结构的时候；产生了很大的疑问。现在总结一下，避免走弯路。

现在的项目结构一般是这样的：


```
$GOPATH
|-- bin
|-- pkg
|-- src
|  |-- github.com
|  |  |-- username
|  |  |  |-- project
|  |  |  |  |-- subpackage
|  |  |  |  |-- main.go 

```
这并不是一成不变的，甚至可以说除了文件一定要放在src目录下。其他一切都是根据公司根据情况自由修改的。

当我在写自己的学习项目时，在import 自定义包的时候，自定义文件时；因为初学没有理解包和函数，文件之间的关系。就老是有不能引用的问题。解决方案是项目结构清晰，自己理解好这三者的关系。

1.包应该是一个功能组件，所有相关的文件都放在同一个包目录下。
2.包当中的函数，如果在同一个包中可以自由调用；但是应该区分好功能性。不然项目结构就会比较混乱。

通常情况下，import的包都是相对`$GOPATH/src`目录引入的，比如从github上面clone下来的项目，直接放到`$GOPATH/src`目录下，就可以直接import。例如：
如果项目的import路径是这样写的：
`import "github.com/yourname/projectname"`
需要将项目代码放置在：
`$GOAPTH/src/github.com/yourname/projectname/`下

如果项目的import是这样写的：
`import "message"`
则将message.go放到:
`$GOAPTH/src/message/`目录下即可。

