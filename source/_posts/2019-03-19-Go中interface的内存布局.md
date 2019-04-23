---
title: Go中interface的内存布局
date: 2019-03-19 20:05:34
category: Go
tags: Go
---

一直以来对interface的底层结构及内存布局不了解，只是单纯的转换和使用。今天就遇到了关于interface内存布局的问题，没有答上来，查了查资料和源码，记录一下。

<!--more-->

### 内存布局
了解interface的内存结构是非常有必要的，只有了解了这一点，我们才能进一步分析诸如类型断言等情况的效率问题。先看一个例子：

```go
type Stringer interface {  
    String() string  
}  
   
type Binary uint64  
   
func (i Binary) String() string {  
    return strconv.FormatUint(i, 10))
}  
   
func (i Binary) Get() uint64 {  
    return uint64(i)  
}  
   
func main() {  
    b := Binary{}  
    s := Stringer(b)  
    fmt.Print(s.String())  
}  
```

interface在内存上实际由两个成员组成，如下图，tab指向虚表，data则指向实际引用的数据。虚表描绘了实际的类型信息及该接口所需要的方法集

![](https://wx3.sinaimg.cn/large/b09f1c9dly1g28ahy06fij208x04dt8p.jpg)

观察itable的结构，首先是描述type信息的一些元数据，然后是满足Stringger接口的函数指针列表（注意，这里不是实际类型Binary的函数指针集哦）。 因此我们如果通过接口进行函数调用，实际的操作其实就是`s.tab->fun[0](s.data)`。 是不是和C++的虚表很像？接下来我们要看看golang的虚表和C++的虚表区别在哪里。

先看C++，它为每种类型创建了一个方法集，而它的虚表实际上就是这个方法集本身或是它的一部分而已，当面临多继承时（或者叫实现多个接口时，这是很常见的），C++对象结构里就会存在多个虚表指针，每个虚表指针指向该方法集的不同部分，因此，C++方法集里面函数指针有严格的顺序。 许多C++新手在面对多继承时就变得紧张，因为它的这种设计方式，为了保证其虚表能够正常工作，C++引入了很多概念，什么虚继承啊，接口函数同名问题啊，同一个接口在不同的层次上被继承多次的问题啊等等…… 就是老手也很容易因疏忽而写出问题代码出来。

我们再来看golang的实现方式，同C++一样，golang也为每种类型创建了一个方法集，不同的是接口的虚表是在运行时专门生成的。 可能细心的同学能够发现为什么要在运行时生成虚表。 因为太多了，每一种接口类型和所有满足其接口的实体类型的组合就是其可能的虚表数量，实际上其中的大部分是不需要的，因此golang选择在运行时生成它，例如，当例子中当首次遇见s := Stringer(b)这样的语句时，golang会生成Stringer接口对应于Binary类型的虚表，并将其缓存。

理解了golang的内存结构，再来分析诸如类型断言等情况的效率问题就很容易了，当判定一种类型是否满足某个接口时，golang使用类型的方法集和接口所需要的方法集进行匹配，如果类型的方法集完全包含接口的方法集，则可认为该类型满足该接口。 例如某类型有$m$个方法，某接口有$n$个方法，则很容易知道这种判定的时间复杂度为$O(m \times n)$，不过可以使用预先排序的方式进行优化，实际的时间复杂度为$O(m+n)$。

### 使用interface的注意事项
- 对象赋值给接口变量时会复制该对象
- 接口使用的是一个名为iface的结构体存储的
    ```go
    type iface struct {
	    tab  *itab // 类型信息 
	    data unsafe.Pointer // 实际对象指针 
    }

    type eface struct {
	    _type *_type
	    data  unsafe.Pointer
    }
    ```
- 只有接口变量内部的两个指针都为nil的时候，接口才等于nil
- interface实际上是一个引用(只保存了两个值)，因此传递它并不会造成太多的损耗


