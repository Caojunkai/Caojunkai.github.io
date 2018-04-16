---
title: Go文件操作
date: 2018-04-17 00:01:16
category: Go
tags: [go,golang,filesystem]
---
Go官方库的文件操作分散在多个包中，比如`os`、`ioutil`包,发现已经有人2015年已经写了一篇这样的文章，写的非常好，我经常在需要使用文件方法的时候回过来查,所以翻译成了中文，记录一下.
原文[ Working with Files in Go](https://www.devdungeon.com/content/working-files-go#write_bytes), 作者[ NanoDano](https://www.devdungeon.com/blogs/nanodano)

## 介绍
#### 万物皆文件
UNIX 的一个基础设计就是"万物皆文件"(everything is a file)。我们不必知道一个文件到底映射成什么，操作系统的设备驱动抽象成文件。操作系统为设备提供了文件格式的接口
Go语言中的reader和writer接口也类似。我们只需简单的读写字节，不必知道reader的数据来自哪里，也不必知道writer将数据发送到哪里。
你可以在`/dev`下查看可用的设备，有些可能需要较高的权限才能访问。

## 基本操作

> 创建空文件

```go
package main
import (
    "log"
    "os"
)
var (
    newFile *os.File
    err     error
)
func main() {
    newFile, err = os.Create("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    log.Println(newFile)
    newFile.Close()
}
```
> Truncate文件

```go
package main
import (
    "log"
    "os"
)
func main() {
    // 裁剪一个文件到100个字节。
    // 如果文件本来就少于100个字节，则文件中原始内容得以保留，剩余的字节以null字节填充。
    // 如果文件本来超过100个字节，则超过的字节会被抛弃。
    // 这样我们总是得到精确的100个字节的文件。
    // 传入0则会清空文件。
    err := os.Truncate("test.txt", 100)
    if err != nil {
        log.Fatal(err)
    }
}
```
> 得到文件信息

```go
package main
import (
    "fmt"
    "log"
    "os"
)
var (
    fileInfo os.FileInfo
    err      error
)
func main() {
    // 如果文件不存在，则返回错误
    fileInfo, err = os.Stat("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("File name:", fileInfo.Name())
    fmt.Println("Size in bytes:", fileInfo.Size())
    fmt.Println("Permissions:", fileInfo.Mode())
    fmt.Println("Last modified:", fileInfo.ModTime())
    fmt.Println("Is Directory: ", fileInfo.IsDir())
    fmt.Printf("System interface type: %T\n", fileInfo.Sys())
    fmt.Printf("System info: %+v\n\n", fileInfo.Sys())
}
```
> 重命名和移动

```go
package main
import (
    "log"
    "os"
)
func main() {
    originalPath := "test.txt"
    newPath := "test2.txt"
    err := os.Rename(originalPath, newPath)
    if err != nil {
        log.Fatal(err)
    }
}
```
> 删除文件

```go
package main
import (
    "log"
    "os"
)
func main() {
    err := os.Remove("test.txt")
    if err != nil {
        log.Fatal(err)
    }
}
```
> 打开和关闭文件

```go
package main
import (
    "log"
    "os"
)
func main() {
    // 简单地以只读的方式打开。下面的例子会介绍读写的例子。
    file, err := os.Open("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    file.Close()
    // OpenFile提供更多的选项。
    // 最后一个参数是权限模式permission mode
    // 第二个是打开时的属性    
    file, err = os.OpenFile("test.txt", os.O_APPEND, 0666)
    if err != nil {
        log.Fatal(err)
    }
    file.Close()
    // 下面的属性可以单独使用，也可以组合使用。
    // 组合使用时可以使用 OR 操作设置 OpenFile的第二个参数，例如：
    // os.O_CREATE|os.O_APPEND
    // 或者 os.O_CREATE|os.O_TRUNC|os.O_WRONLY
    // os.O_RDONLY // 只读
    // os.O_WRONLY // 只写
    // os.O_RDWR // 读写
    // os.O_APPEND // 往文件中添建（Append）
    // os.O_CREATE // 如果文件不存在则先创建
    // os.O_TRUNC // 文件打开时裁剪文件
    // os.O_EXCL // 和O_CREATE一起使用，文件不能存在
    // os.O_SYNC // 以同步I/O的方式打开
}
```
> 检查文件是否存在

```go
package main
import (
    "log"
    "os"
)
var (
    fileInfo *os.FileInfo
    err      error
)
func main() {
    // 文件不存在则返回error
    fileInfo, err := os.Stat("test.txt")
    if err != nil {
        if os.IsNotExist(err) {
            log.Fatal("File does not exist.")
        }
    }
    log.Println("File does exist. File information:")
    log.Println(fileInfo)
}
```
> 检查读写权限

```go
package main
import (
    "log"
    "os"
)
func main() {
    // 这个例子测试写权限，如果没有写权限则返回error。
    // 注意文件不存在也会返回error，需要检查error的信息来获取到底是哪个错误导致。
    file, err := os.OpenFile("test.txt", os.O_WRONLY, 0666)
    if err != nil {
        if os.IsPermission(err) {
            log.Println("Error: Write permission denied.")
        }
    }
    file.Close()
    // 测试读权限
    file, err = os.OpenFile("test.txt", os.O_RDONLY, 0666)
    if err != nil {
        if os.IsPermission(err) {
            log.Println("Error: Read permission denied.")
        }
    }
    file.Close()
}
```

> 改变权限、拥有者、时间戳

```go
package main
import (
    "log"
    "os"
    "time"
)
func main() {
    // 使用Linux风格改变文件权限
    err := os.Chmod("test.txt", 0777)
    if err != nil {
        log.Println(err)
    }
    // 改变文件所有者
    err = os.Chown("test.txt", os.Getuid(), os.Getgid())
    if err != nil {
        log.Println(err)
    }
    // 改变时间戳
    twoDaysFromNow := time.Now().Add(48 * time.Hour)
    lastAccessTime := twoDaysFromNow
    lastModifyTime := twoDaysFromNow
    err = os.Chtimes("test.txt", lastAccessTime, lastModifyTime)
    if err != nil {
        log.Println(err)
    }
}
```
> 硬链接和软链接

一个普通的文件是一个指向硬盘的inode的地方。
硬链接创建一个新的指针指向同一个地方。只有所有的链接被删除后文件才会被删除。硬链接只在相同的文件系统中才工作。你可以认为一个硬链接是一个正常的链接。

symbolic link，又叫软连接，和硬链接有点不一样，它不直接指向硬盘中的相同的地方，而是通过名字引用其它文件。他们可以指向不同的文件系统中的不同文件。并不是所有的操作系统都支持软链接。

```go
package main
import (
    "os"
    "log"
    "fmt"
)
func main() {
    // 创建一个硬链接。
    // 创建后同一个文件内容会有两个文件名，改变一个文件的内容会影响另一个。
    // 删除和重命名不会影响另一个。
    err := os.Link("original.txt", "original_also.txt")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("creating sym")
    // Create a symlink
    err = os.Symlink("original.txt", "original_sym.txt")
    if err != nil {
        log.Fatal(err)
    }
    // Lstat返回一个文件的信息，但是当文件是一个软链接时，它返回软链接的信息，而不是引用的文件的信息。
    // Symlink在Windows中不工作。
    fileInfo, err := os.Lstat("original_sym.txt")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Link info: %+v", fileInfo)
    //改变软链接的拥有者不会影响原始文件。
    err = os.Lchown("original_sym.txt", os.Getuid(), os.Getgid())
    if err != nil {
        log.Fatal(err)
    }
}
```