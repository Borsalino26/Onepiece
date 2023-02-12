---
title: Golang：2. 程序结构
date: 2023-02-10 22:11:57
categories:
    - Golang
tags:
    - Golang
---
> 本文以一个简单的Go程序为例，介绍Go程序的基本结构。
<!-- more -->
## 1. 一个简单的Go程序
Go程序的一般结构，主要分为四段：
* `包声明`：表明文件属于哪个包
* `import声明`：导入程序需要的包
* `包级别的声明`：声明属于当前包的一些内容，主要有：`常量(const)`、`变量(var)`、`类型(type)`、`函数(func)`

我们在前文`Hello World`程序的基础上稍作修改，得到下面一段声明了1个`常量`、2个`变量`和2个`函数`的程序
文件名 golang-2.go
```go
// 1. 包声明 
package main

// 2. import声明
import (
	"fmt"
)

// 3. 包级别的声明

//// 3.1 常量
const greeting = "Hello World!"

//// 3.2 变量
var firstName = "Bob"

//// 3.3 函数
func sayHello(name string) {
	fmt.Println(name, ": ", greeting)
}

//// 3.3 函数
func main() {
	var lastName string
	lastName = "Pop"
	sayHello(firstName + " " + lastName)
}
```
运行输出
```shell
E:\Notes\Golang> go run golang-2.go
Bob Pop :  Hello World!
```

## 2. 包和文件
* 前面Go程序我们首先通过`package`语句进行了**包声明**操作，来声明`golang-2.go`文件属于`main`包。
```go
// 1. 包声明 
package main
```
在Go语言中，包的作用类似于其它语言的模块/库，用于支持模块化、封装、复用。一个包由一个或多个.go文件组成，例如前面我们的`main`包便是由`golang-2.go`组成


* 随后我们进行了**import声明**：使用`import`语句导入需要用到的包

``` go
import (
	"fmt"
)
```
程序中需要用到的外部包都要通过`import`语句来导入，依赖多个外部包的情况如下：
```go
import (
	"errors"

	"github.com/cloudwego/kitex/internal/server"
	"github.com/cloudwego/kitex/pkg/remote"
	"github.com/cloudwego/kitex/pkg/remote/bound"
	"github.com/cloudwego/kitex/pkg/remote/trans/invoke"
	info "github.com/cloudwego/kitex/pkg/serviceinfo"
)
```
注意到前面的代码`"github.com/cloudwego/kitex/pkg/serviceinfo"`前多了一个`info`，这是包的短名字

我们可以给导入的包指定一个**短名字**，之后的代码中，便可以使用这个短名字来引用包的内容

例如前面的例子中，我们便可以使用`info`，而不再是`serviceinfo`来引用`"github.com/cloudwego/kitex/pkg/serviceinfo"`包的内容


## 3. 名称
Go语言的命名规则很简单：名称开头是一个字母或下划线，后面可以跟任意数量的字母、数字、下划线。

不过，Go中有25个**关键字**，不能直接用于名称：
```
break       default     func    interface   select
case        defer       go      map         struct
chan        else        goto    package     switch
const       fallthrough if      range       type
continue    for         import  return      var
```
另外，**预声明**的常量、类型和函数也不能用于名称，比如：
```
常量： true、false、itoa、nil
类型： int、float、bool、byte...
函数： make、len、cap...
```
并且，Go程序员在命名上更偏好使用 “驼峰式”命名：用大写字母来区分不同的单词，而不是下划线_。例如前文的两个变量
```go
var firstName = "Bob"
var lastName string
```

## 4. 变量的声明与赋值
### 4.1 var声明
`var`声明创建一个具体类型的变量，然后给它附加一个名字，设置初始值，格式如下
```
var name type = expression
```
其中，类型和表达式可以省略一个，但不能都省略：
* 省略类型，则类型由表达式决定，例如：
``` go
func main() {
    // s的类型为string
    var s = "gogogo"
    // i的类型为int
    var i = 128

    fmt.Printf("s的类型为:%T\n", s)
    fmt.Printf("i的类型为:%T\n", i) // %T为fmt包的格式化占位符，用于输出变量的类型
}
```
程序输出：
```shell
s的类型为:string
i的类型为:int
```
* 表达式省略，则变量初始值为相应类型的零值，例如：
``` go
func main() {
    // s的值为""
    var s string
    // i的值为0
    var i int
    // b的值为false
    var b bool

    fmt.Printf("s的值为:%v\n", s)
    fmt.Printf("i的值为:%v\n", i)
    fmt.Printf("b的值为:%v\n", b)
}
```
程序输出：
```shell
s的值为:
i的值为:0
b的值为:false
```

### 4.2 短变量
在函数中，可以使用**短变量**来声明和初始化局部变量，相较于**var声明**更为短小灵活，其格式为：
```
name := expression
```
其中，`name`的类型由expression决定，如下：
```go
func main() {
	s := "Golang"
	b := true
	fmt.Printf("value:%v, type:%T\n", s, s)
	fmt.Printf("value:%v, type:%T\n", b, b)
}
```
程序输出：
```
value:Golang, type:string
value:true, type:bool
```

### 4.3 new函数
我们还可以使用Go语言内置的new函数来声明变量，格式为：
```
name = new(type)
```
不过new函数返回值并不是新生成的变量本身，而是其地址。new(type)表达式会创建一个type类型的变量，初始化为type类型的零值，并返回其地址。例如：
``` go
func main() {
	s := new(string)
	b := new(bool)
	fmt.Printf("value:%v, type:%T\n", s, s)
	fmt.Printf("value:%v, type:%T\n", b, b)
}
```
程序输出：
```
value:0xc000060050, type:*string
value:0xc00000e098, type:*bool
```

### 4.4 赋值
Go的赋值语句很简单`var = value`，值得一提的是，Go支持`多重赋值`：
```go
func main() {
	x := "XX"
	y := "YY"
	x, y = y, x
	fmt.Printf("x: %v, y: %v\n", x, y)
}
```
程序输出：
```
x: YY, y: XX
```