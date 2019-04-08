---
title: "String和strconv包"
date: 2018-12-29T14:25:38+08:00

tags: 
  - golang
  - strconv
---

字符串是我们使用最多的部分，所以需要好好熟悉一下。其中[strconv包](https://golang.org/pkg/strconv/)主要是实现基本数据类型与字符串之间的转换，是每天都要用到的。所以要好好学习。

## 字符串和其他类型转换


```
a := 100
str := fmt.Sprintf("%d",a)
```
> 其实这里就使用字符串的格式化输出。
## 字符串切片


```
str := "hello world"
substr := str[0:5]
```

## 字符串多行显示

## 字符串其他功能

在strings包中查看系统本身提供的功能。[查看](https://go-zh.org/pkg/strings/)

