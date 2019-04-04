---
title: "Go基础 闭包"
date: 2019-03-05T10:01:17+08:00
draft: true
tags: 
  - golang
  - blockchain
---



# 闭包

闭包是由函数及其相关引用环境组合而成的实体(即：闭包=函数+引用环境)。

~~~

func main()  {

	fmt.Println("Go studyNote start")

	//闭包
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(pos(i), neg(-2*i))
	}



}

//闭包
func adder() func(int) int  {

	sum := 0
	innerfunc := func(x int)int {
		sum += x
		return sum
	}

	return innerfunc
}

运行结果：
Go studyNote start
0 0
1 -2
3 -6
6 -12
10 -20
15 -30
21 -42
28 -56
36 -72
45 -90
~~~

注意:Go不能在函数内部显式嵌套定义函数，但是可以定义一个匿名函数。如上面所示，我们定义了一个匿名函数对象，然后将其赋值给innerfunc，最后将其作为返回值返回。

当用不同的参数调用adder函数得到（pos(i)，neg(i)）函数时，得到的结果是隔离的，也就是说每次调用adder返回的函数都将生成并保存一个新的局部变量sum。其实这里adder函数返回的就是闭包。
这个就是Go中的闭包，一个函数和与其相关的引用环境组合而成的实体。一句关于闭包的名言: **对象是附有行为的数据，而闭包是附有数据的行为**。


其实理解闭包最方便的方法就是将闭包函数看成一个类，一个闭包函数调用就是实例化一个类（在Objective-c中闭包就是用类来实现的），然后就可以从类的角度看出哪些是“全局变量”，哪些是“局部变量”。例如在例子中，pos和neg分别实例化了两个“闭包类”，在这个“闭包类”中有个“闭包全局变量”sum。所以这样就很好理解返回的结果了。

## 闭包使用：

其实理解闭包最方便的方法就是将闭包函数看成一个类，一个闭包函数调用就是实例化一个类（在Objective-c中闭包就是用类来实现的），然后就可以从类的角度看出哪些是“全局变量”，哪些是“局部变量”。例如在第一个例子中，pos和neg分别实例化了两个“闭包类”，在这个“闭包类”中有个“闭包全局变量”sum。所以这样就很好理解返回的结果了。


```

package main
 
import (
    "errors"
    "fmt"
)
 
type Traveser func(ele interface{})
/*
    Process:封装公共切片数组操作
*/
func Process(array interface{}, traveser Traveser) error {
 
    if array == nil {
        return errors.New("nil pointer")
    }
    var length int //数组的长度
    switch array.(type) {
    case []int:
        length = len(array.([]int))
    case []string:
        length = len(array.([]string))
    case []float32:
        length = len(array.([]float32))
        default:
        return errors.New("error type")
    }
    if length == 0 {
        return errors.New("len is zero.")
    }
    traveser(array)
    return nil
}
/*
    具体操作:升序排序数组元素
*/
func SortByAscending(ele interface{}) {
    intSlice, ok := ele.([]int)
    if !ok {
        return
    }
    length := len(intSlice)
 
    for i := 0; i < length-1; i++ {
        isChange := false
        for j := 0; j < length-1-i; j++ {
 
            if intSlice[j] > intSlice[j+1] {
                isChange = true
                intSlice[j], intSlice[j+1] = intSlice[j+1], intSlice[j]
            }
        }
 
        if isChange == false {
            return
        }
 
    }
}
/*
    具体操作:降序排序数组元素
*/
func SortByDescending(ele interface{}) {
 
    intSlice, ok := ele.([]int)
    if !ok {
        return
    }
    length := len(intSlice)
    for i := 0; i < length-1; i++ {
        isChange := false
        for j := 0; j < length-1-i; j++ {
            if intSlice[j] < intSlice[j+1] {
                isChange = true
                intSlice[j], intSlice[j+1] = intSlice[j+1], intSlice[j]
            }
        }
 
        if isChange == false {
            return
        }
 
    }
}
 
func main() {
 
    intSlice := make([]int, 0)
    intSlice = append(intSlice, 3, 1, 4, 2)
 
    Process(intSlice, SortByDescending)
    fmt.Println(intSlice) //[4 3 2 1]
    Process(intSlice, SortByAscending)
    fmt.Println(intSlice) //[1 2 3 4]
 
    stringSlice := make([]string, 0)
    stringSlice = append(stringSlice, "hello", "world", "china")
 
    /*
       具体操作:使用匿名函数封装输出操作
    */
    Process(stringSlice, func(elem interface{}) {
 
        if slice, ok := elem.([]string); ok {
            for index, value := range slice {
                fmt.Println("index:", index, "  value:", value)
            }
        }
    })
    floatSlice := make([]float32, 0)
    floatSlice = append(floatSlice, 1.2, 3.4, 2.4)
 
    /*
       具体操作:使用匿名函数封装自定义操作
    */
    Process(floatSlice, func(elem interface{}) {
 
        if slice, ok := elem.([]float32); ok {
            for index, value := range slice {
                slice[index] = value * 2
            }
        }
    })
    fmt.Println(floatSlice) //[2.4 6.8 4.8]
}


输出结果:

[4 3 2 1]   
[1 2 3 4]
index: 0   value: hello
index: 1   value: world
index: 2   value: china
[2.4 6.8 4.8]


```

在上面的例子中，Process函数负责对切片(数组）数据进行操作，在操作切片(数组)时候，首先要做一些参数检测，例如指针是否为空、数组长度是否大于0等。这些是操作数据的公共操作。具体针对数据可以有自己特殊的操作，包括排序(升序、降序）、输出等。针对这些特殊的操作可以使用函数对象来进行封装。

## 为什么使用闭包？

说穿了，就是闭包是为了解决什么问题而产生的技术呢？？

闭包 = 函数 + 引用环境

往往使用闭包是解决了一些变量的定义，主要就是全局变量的使用。

