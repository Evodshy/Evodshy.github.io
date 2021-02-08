---

title: go语言学习之路

author: evod

tags: go  

key: go_blog

---

# go语言学习之路 ------ 语法知识杂谈

```
  学习go语言一段时间了,主要是了解go的一些语法知识,像是基础数据类型,控制语句等,写篇博客回顾一下，顺便练习一下Markdown语法。因为基础语法和其他语言大差不差，核心的一些还没掌握，就选取几点我觉得比较有特点的简单梳理一下，其他待日后补足。  
```

###        go语言的包机制

​        go语言是以包的形式来管理文件和项目目录结构的,**包**是**函数**和**数据**的集合。用 package 关键字定义一个包。文件名不需要与包名一致。包名的约定是使用小写字符。每个文件都是属于一个包,一个包可以由多个文件组成

```
packeage 包名 // 声明当前文件所属的包

import (  // 引入一个包,官方提供的包和自定义的包都可以,不过自定义的包要写清楚实际的路径
    "包名" 
)
```

​        引入包之后就可以在当前文件中使用该包中的函数和数据

```
包名.变量名
包名.函数名(参数...)
```

​        有一点要注意,go中根据首字母的大小写来确定可以访问的权限。无论是方法名、常量、变量名还是结构体的名称，如果首字母大写，则可以被其他的包访问；如果首字母小写，则只能在本包中使用可以简单的理解成，首字母大写是公有的，首字母小写是私有的

### go语言数据类型

go语言的数据类型可大致分为值类型和引用类型两种。

- **值类型包括**：整型，浮点，字符串，数组，bool，struct，声明的变量通常分配栈区的内存空间，这些变量所占据的空间在函数被调用完毕后自动释放
- **引用类型包括**：channel，map，slice，指针，这些类型的数据内存通常在堆上分配。当没用变量关联到该内存空间时，会被go语言的回收机制释放掉

实际上go语言存在逃逸分析的机制，编译器会自动选择把变量放在堆区或是栈区，看下面的代码
 

```
package main
import (
    "fmt"
)
func newInt() *int {
    var i int =4
    fmt.Println(&i)
    return &i  
}
func main() {
    someInt := newInt()
    fmt.Println(someInt)
    fmt.Println(*someInt)//这里不会像C，报错段错误提示，而是成功返回变量的值
}

输出结果：
0xc000016060
0xc000016060
4
```

newInt函数返回一个临时变量的指针给main函数，一般来说临时变量存储在栈中，内存空间会随函数结束一并回收，再访问该地址空间会报段错误，这里却可以成功获取到存储的值，显然变量i是存储在堆中。看网上博客解释，go编译器会根据变量作用域范围来考虑在哪里分配内存，且配合go语言的内存回收机制，不用担心内存泄漏问题。具体可看这篇博客[GO语言局部变量堆、栈分析](https://blog.csdn.net/sunansheng/article/details/89520428)


### go语言的一些特殊编码规范

- 每行若只有一条语句可以不用加分号,但是若置多条语句于一行需用分号间隔开
- 大括号需跟在表达式后,统一编码风格

```golang
if x > 0 {       // { 是强制的
    return y
} else {
    return x 
}
```

- go不支持隐式的类型转换,不同类型之间需要强制转换类型

```
i int32 = 1
j int64 = (int64)i

```

- 函数的声明:  与C的函数声明最大不同的一点是, go的返回值可以有多个

```
//关键字func 函数名 (参数...)(返回值...) {}
func IndexRune(s string, r rune) (r,s int) { 
    //函数体
}
```