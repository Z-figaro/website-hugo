---
title: "Go基础——结构体"
date: 2019-04-09T09:42:49+08:00
draft: true
tags: 
  - golang
  
---

# Struct 结构体

* struct 是值类型，
* 在go中没有class类型，用struct代替，
* struct 的内存是连续的。


## 声明方式： 


```
type 结构体名称 struct {
    //属性有哪些
    //属性名 属性类型
	name string
	
} 
```

使用：
var 变量名 结构体名称（其实就是当这是一种类型）
var head *student = new(student)
                  = &student
                  
结构体的方法：
 
```
在方法名前面指定类型，这在go 中表明这个函数是 list结构体的方法接收器。因为go语言没有类，所以方法都定义在结构上了。

func (list *List) Append(node *Node) {
}
```
## 链表

每一种数据结构都是现实的抽象。

```
type Student struct{
        name string
        Next* Student
}
```

单向和双向链表。

对于链表的[介绍](https://segmentfault.com/a/1190000011744981)，[这里](https://studygolang.com/articles/12686)写的非常详细了。我就不抄了。记录一些问题。



