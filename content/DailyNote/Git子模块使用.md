---
title: "Git子模块使用"
date: 2019-04-08T14:27:07+08:00
draft: false
tags: 
  - git
  - submodule
---

# 使用场景

我的blog是使用的hugo创建，其中的主题，我是直接网上找的。这个主题本身也是一个gh上的项目。我如何直接用git同时管理我们的Blog 和 这个blog设定的主题呢？？？

这里就是针对git仓库下的某个类库文件也作为一个git仓库进行管理的技术，submodule，git的子模块。
项目的组件化结合pod应该也可以使用这个技术。

## submodule的添加

在项目的主目录操作
> git submodule add git@github.com:jjz/pod-library.git pod-library

可以查看仓库的状态

> git status

```
 On branch master
    Changes to be committed:
    
        new file:   .gitmodules
        new file:   pod-library
```
在隐藏文件.gitmodules 中，你可以看到

```
[submodule "pod-library"]
        path = pod-library
        url = git@github.com:jjz/pod-library.git
```
还有一种添加方法：


```
git add .gitmodules pod-ibrary
git commit -m "pod-library submodule"
git submodule init
```

## 删除

*  删除子模块文件夹，比如文件叫assets

>  git rm --cached assets
>  rm -rf assets
>  git rm -r assets （添加目录作为子模块）

* 删除.gitmodules文件中相关子模块信息

>     vim .gitmodules
> 

* 删除.git/config中的相关子模块信息

>     vim .git/config
> 

* 删除.git文件夹中的相关子模块文件

>     rm -rf .git/modules/

最后提交修改就好了。


## 修改

> cd pod-library

在这个文件夹下面进行相应的 add，commit，push等等操作，这就是对子模块的修改添加到对应的子模块仓库了。然后主模块，你会发现就发生了相应的修改，需要你推送。

## 更新
在父目录
> git submodule foreach git pull

在子目录
>cd pod-library
>git pull


## clone项目和子模块

1. 采用递归clone整个项目
> git clone git@github.com:jjz/pod-project.git --recursive

2. 父项目先clone，然后进入子模块
> cd pod-project
> git submodule init
> git submodule update



