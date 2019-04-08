---
title: "Go语言基础"
date: 2018-12-19T15:38:14+08:00

featuredImg: ""
tags: 
  - go
---
go 的所有基础知识，根据自己的情况，不定时添加。更深入的内容会单开文章来写。
<!--more-->
# go语言基础

## GO语言特性
1.不用做内存管理
2.天然支持高并发
3.channel管道
    管道最初是unix中的特性，因为进程之间是隔离的，如果两个进程之间需要资源交互；那么就把资源放到管道中。通过管道交互。
4.多返回值
## Go 语言的包引入一般为: 项目名/包名

`import "test/controllers"`
方法的调用为: 包名.方法名()

`controllers.Test()`
本包内方法名可为小写，包外调用方法名首字母必须为大写。


## fmt包


1.**Print()** 函数将参数列表 a 中的各个参数转换为字符串并写入到标准输出中。

非字符串参数之间会添加空格，返回写入的字节数。

`func Print(a ...interface{}) (n int, err error)`

2.**Println()** 函数功能类似 Print，只不过最后会添加一个换行符。

所有参数之间会添加空格，返回写入的字节数。

`func Println(a ...interface{}) (n int, err error)`

3.***Printf()*** 函数将参数列表 a 填写到格式字符串 format 的占位符中。

填写后的结果写入到标准输出中，返回写入的字节数。

`func Printf(format string, a ...interface{}) (n int, err error)`

实例：


```
   fmt.Print("a", "b", 1, 2, 3, "c", "d", "\n")
	fmt.Println("a", "b", 1, 2, 3, "c", "d")
	fmt.Printf("ab %d %d %d cd\n", 1, 2, 3)
	fmt.Println("你好")
	
    ab1 2 3cd
    a b 1 2 3 c d
    ab 1 2 3 cd
    你好
```

## 条件控制
1. 支持多条件匹配

```
switchswitch{{
         casecase  11,,22,,33,,44::
         defaultdefault::
 }}
```
2. 不同的 case 之间不使用 break 分隔，默认只会执行一个 case。

3. 如果想要执行多个 case，需要使用 fallthrough 关键字，也可用 break 终止。

```
switch{
    case 1:
    ...
    if(...){
        break
    }

    fallthrough // 此时switch(1)会执行case1和case2，但是如果满足if条件，则只执行case1

    case 2:
    ...
    case 3:
}
```

### select 语句

select是Go中的一个控制结构，类似于用于通信的switch语句。每个case必须是一个通信操作，要么是发送要么是接收。

select随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。一个默认的子句应该总是可运行的。

以下描述了 select 语句的语法：

每个case都必须是一个通信
所有channel表达式都会被求值
所有被发送的表达式都会被求值
如果任意某个通信可以进行，它就执行；其他被忽略。
如果有多个case都可以运行，Select会随机公平地选出一个执行。其他不会执行。 
否则：
如果有default子句，则执行该语句。
如果没有default字句，select将阻塞，直到某个通信可以运行；Go不会重新对channel或值进行求值。


```
package main

import "fmt"

func main() {
   var c1, c2, c3 chan int
   var i1, i2 int
   select {
      case i1 = <-c1:
         fmt.Printf("received ", i1, " from c1\n")
      case c2 <- i2:
         fmt.Printf("sent ", i2, " to c2\n")
      case i3, ok := (<-c3):  // same as: i3, ok := <-c3
         if ok {
            fmt.Printf("received ", i3, " from c3\n")
         } else {
            fmt.Printf("c3 is closed\n")
         }
      default:
         fmt.Printf("no communication\n")
   }    
}
```

## 函数


```
func function_name( [parameter list] ) [return_types] {
   函数体
}
```
* func：函数由 func 开始声明
* function_name：函数名称，函数名和参数列表一起构成了函数签名。
* parameter list：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，这个值被称为实际参数。参数列表指定的是参数类型、顺序、及参数个数。参数是可选的，也就是说函数也可以不包含参数。
* return_types：返回类型，函数返回一列值。return_types 是该列值的数据类型。有些功能不需要返回值，这种情况下 return_types 不是必须的。
* 函数体：函数定义的代码集合。

实例：


```
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
   var ret int

   /* 调用函数并返回最大值 */
   ret = max(a, b)

   fmt.Printf( "最大值是 : %d\n", ret )
}

/* 函数返回两个数的最大值 */
func max(num1, num2 int) int {
   /* 定义局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result 
}
```

## 全局变量与局部变量

Go 语言程序中全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑。

```
package main

import "fmt"

/* 声明全局变量 */
var g int = 20

func main() {
   /* 声明局部变量 */
   var g int = 10

   fmt.Printf ("结果： g = %d\n",  g)
}
```

一个更好的实例：

```
package main

import "fmt"

/* 声明全局变量 */
var a int = 20

func main() {
    /* main 函数中声明局部变量 */
    var a int = 10
    var b int = 20
    var c int = 0

    fmt.Printf("main()函数中 a = %d\n", a)
    c = sum(a, b)
    fmt.Printf("main()函数中 a = %d\n", a)
    fmt.Printf("main()函数中 c = %d\n", c)
}

/* 函数定义-两数相加 */
func sum(a, b int) int {
    a = a + 1
    fmt.Printf("sum() 函数中 a = %d\n", a)
    fmt.Printf("sum() 函数中 b = %d\n", b)
    return a + b
}
```
结果：

```
main()函数中 a = 10
sum() 函数中 a = 11
sum() 函数中 b = 20
main()函数中 a = 10
main()函数中 c = 31
```

## 切片

Go 语言切片是对数组的抽象。

Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go中提供了一种灵活，功能强悍的内置类型切片("动态数组"),与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。

定义：
`var identifier []type`

简写：
> var slice1 []type = make([]type, len)

> 也可以简写为

> slice1 := make([]type, len)

也可以指定容量，其中capacity为可选参数。
> make([]T, length, capacity)

### 切片初始化

> s :=[] int {1,2,3 } 
直接初始化切片，[]表示是切片类型，{1,2,3}初始化值依次是1,2,3.其cap=len=3

> s := arr[:] 
初始化切片s,是数组arr的引用

> s := arr[startIndex:endIndex] 
将arr中从下标startIndex到endIndex-1 下的元素创建为一个新的切片

> s := arr[startIndex:] 
缺省endIndex时将表示一直到arr的最后一个元素

> s := arr[:endIndex] 
缺省startIndex时将表示从arr的第一个元素开始

> s1 := s[startIndex:endIndex] 
通过切片s初始化切片s1

> s :=make([]int,len,cap) 
通过内置函数make()初始化切片s,[]int 标识为其元素类型为int的切片

### len() 和 cap() 函数

切片是可索引的，并且可以由 len() 方法获取长度。

切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。

实例：

```
package main

import "fmt"

func main() {
   var numbers = make([]int,3,5)

   printSlice(numbers)
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}

结果：
len=3 cap=5 slice=[0 0 0]
```

### 切片截取

可以通过设置下限及上限来设置截取切片 [lower-bound:upper-bound]，实例如下

```
package main

import "fmt"

func main() {
   /* 创建切片 */
   numbers := []int{0,1,2,3,4,5,6,7,8}   
   printSlice(numbers)

   /* 打印原始切片 */
   fmt.Println("numbers ==", numbers)

   /* 打印子切片从索引1(包含) 到索引4(不包含)*/
   fmt.Println("numbers[1:4] ==", numbers[1:4])

   /* 默认下限为 0*/
   fmt.Println("numbers[:3] ==", numbers[:3])

   /* 默认上限为 len(s)*/
   fmt.Println("numbers[4:] ==", numbers[4:])

   numbers1 := make([]int,0,5)
   printSlice(numbers1)

   /* 打印子切片从索引  0(包含) 到索引 2(不包含) */
   number2 := numbers[:2]
   printSlice(number2)

   /* 打印子切片从索引 2(包含) 到索引 5(不包含) */
   number3 := numbers[2:5]
   printSlice(number3)

}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}

结果：
len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
numbers == [0 1 2 3 4 5 6 7 8]
numbers[1:4] == [1 2 3]
numbers[:3] == [0 1 2]
numbers[4:] == [4 5 6 7 8]
len=0 cap=5 slice=[]
len=2 cap=9 slice=[0 1]
len=3 cap=7 slice=[2 3 4]
```
### append() 和 copy() 函数

如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。

下面的代码描述了从拷贝切片的 copy 方法和向切片追加新元素的 append 方法。


```
package main

import "fmt"

func main() {
   var numbers []int
   printSlice(numbers)

   /* 允许追加空切片 */
   numbers = append(numbers, 0)
   printSlice(numbers)

   /* 向切片添加一个元素 */
   numbers = append(numbers, 1)
   printSlice(numbers)

   /* 同时添加多个元素 */
   numbers = append(numbers, 2,3,4)
   printSlice(numbers)

   /* 创建切片 numbers1 是之前切片的两倍容量*/
   numbers1 := make([]int, len(numbers), (cap(numbers))*2)

   /* 拷贝 numbers 的内容到 numbers1 */
   copy(numbers1,numbers)
   printSlice(numbers1)   
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}

结果：
len=0 cap=0 slice=[]
len=1 cap=1 slice=[0]
len=2 cap=2 slice=[0 1]
len=5 cap=6 slice=[0 1 2 3 4]
len=5 cap=12 slice=[0 1 2 3 4]

```

### 范围Range
Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对的 key 值。

## map

Map 是一种无序的键值对的集合。Map 最重要的一点是通过 key 来快速检索数据，key 类似于索引，指向数据的值。

Map 是一种集合，所以我们可以像迭代数组和切片那样迭代它。不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 Map 是使用 hash 表来实现的。

/* 声明变量，默认 map 是 nil */
> var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
> map_variable := make(map[key_data_type]value_data_type)


**如果不初始化 map，那么就会创建一个 nil map。nil map 不能用来存放键值对**

实例：

```
package main

import "fmt"

func main() {
    var countryCapitalMap map[string]string /*创建集合 */
    countryCapitalMap = make(map[string]string)

    /* map插入key - value对,各个国家对应的首都 */
    countryCapitalMap [ "France" ] = "Paris"
    countryCapitalMap [ "Italy" ] = "罗马"
    countryCapitalMap [ "Japan" ] = "东京"
    countryCapitalMap [ "India " ] = "新德里"

    /*使用键输出地图值 */ for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [country])
    }

    /*查看元素在集合中是否存在 */
    captial, ok := countryCapitalMap [ "美国" ] /*如果确定是真实的,则存在,否则不存在 */
    /*fmt.Println(captial) */
    /*fmt.Println(ok) */
    if (ok) {
        fmt.Println("美国的首都是", captial)
    } else {
        fmt.Println("美国的首都不存在")
    }
}

结果：
France 首都是 Paris
Italy 首都是 罗马
Japan 首都是 东京
India  首都是 新德里
美国的首都不存在
```

### delete() 函数


```
package main

import "fmt"

func main() {
    /* 创建map */
    countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}

    fmt.Println("原始地图")

    /* 打印地图 */
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [ country ])
    }

    /*删除元素*/ delete(countryCapitalMap, "France")
    fmt.Println("法国条目被删除")

    fmt.Println("删除元素后地图")

    /*打印地图*/
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [ country ])
    }
}

结果：
原始地图
India 首都是 New delhi
France 首都是 Paris
Italy 首都是 Rome
Japan 首都是 Tokyo
法国条目被删除
删除元素后地图
Italy 首都是 Rome
Japan 首都是 Tokyo
India 首都是 New delhi
```

## 递归函数


```
func recursion() {
   recursion() /* 函数调用自身 */
}

func main() {
   recursion()
}
```
Go 语言支持递归。但我们在使用递归时，开发者需要设置退出条件，否则递归将陷入无限循环中。

递归函数对于解决数学上的问题是非常有用的，就像计算阶乘，生成斐波那契数列等


```
package main

import "fmt"

func fibonacci(n int) int {
  if n < 2 {
   return n
  }
  return fibonacci(n-2) + fibonacci(n-1)
}

func main() {
    var i int
    for i = 0; i < 10; i++ {
       fmt.Printf("%d\t", fibonacci(i))
    }
}

结果：
0    1    1    2    3    5    8    13    21    34
```

## 类型转换

> type_name(expression)

**type_name 为类型，expression 为表达式。**


```
package main

import "fmt"

func main() {
   var sum int = 17
   var count int = 5
   var mean float32
   
   mean = float32(sum)/float32(count)
   fmt.Printf("mean 的值为: %f\n",mean)
}

结果：
mean 的值为: 3.400000
```

## 接口
Go 语言提供了另外一种数据类型即接口，它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。

表达：

```
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

实例：

```
package main

import (
    "fmt"
)

type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}

在上面的例子中，我们定义了一个接口Phone，接口里面有一个方法call()。然后我们在main函数里面定义了一个Phone类型变量，并分别为之赋值为NokiaPhone和IPhone。然后调用call()方法，输出结果如下：

I am Nokia, I can call you!
I am iPhone, I can call you!
```

##  错误处理


```
type error interface {
    Error() string
}
```
这是定义。


```
package main

import (
    "fmt"
)

// 定义一个 DivideError 结构
type DivideError struct {
    dividee int
    divider int
}

// 实现 `error` 接口
func (de *DivideError) Error() string {
    strFormat := `
    Cannot proceed, the divider is zero.
    dividee: %d
    divider: 0
`
    return fmt.Sprintf(strFormat, de.dividee)
}

// 定义 `int` 类型除法运算的函数
func Divide(varDividee int, varDivider int) (result int, errorMsg string) {
    if varDivider == 0 {
        dData := DivideError{
            dividee: varDividee,
            divider: varDivider,
        }
        errorMsg = dData.Error()
        return
    } else {
        return varDividee / varDivider, ""
    }

}

func main() {

    // 正常情况
    if result, errorMsg := Divide(100, 10); errorMsg == "" {
        fmt.Println("100/10 = ", result)
    }
    // 当被除数为零的时候会返回错误信息
    if _, errorMsg := Divide(100, 0); errorMsg != "" {
        fmt.Println("errorMsg is: ", errorMsg)
    }

}

结果：
100/10 =  10
errorMsg is:  
    Cannot proceed, the divider is zero.
    dividee: 100
    divider: 0
```

