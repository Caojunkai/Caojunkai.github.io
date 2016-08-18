---
layout : post
title : Linux与Windows的断行符问题
category : Linux
tags : Linux
---

在Linux系统中，当我们使用`cat -A`命令来查看以Windows建立的文件的格式时，可以发现在Windows中使用的断行符是`^M$`,被称为“CRLF”。而以Linux建立的文件格式却只有LF($)这个断行符号。

Linux下面的命令在开始执行时，他的判断依据是[ENTER],而且Linux的[ENTER]为LF符号。由于Windows的断行符是CRLF，也就是说多出了一个`^M`的符号来。这时候，如果去执行一个shell script的脚本文件时，可能就会出现问题。

<!--more-->

那么，该怎么办呢？很简单，把格式转换为Linux格式即可，难道要vim进入该文件，一个一个去删除`^M`字符吗，肯定不是拉，我们可以使用命令来进行格式转换。

    dos2UNIX [-kn] file [newfile]
    UNIX2dos [-kn] file [newfile]

参数：
	
	-k:保留原本文件的mtime格式
	-n:保留原本的文件，将转换后的文件输出到新文件 [如：dos2UNIX -n oldfile newfile]

下面将从Windows传到Linux的hello.sh文件进行格式的转换：

    hanson@linux-host:/tmp$ dos2unix -k -n hello.sh new.hello.sh
    dos2unix: converting file hello.sh to file new.hello.sh in Unix format ...

使用`ls -l`查看下：

    hanson@linux-host:/tmp$ ls -l
    -rwxr-xr-x 1 hanson hanson   59  6月 27 20:22 hello.sh
    -rwxr-xr-x 1 hanson hanson   50  6月 27 20:25 new.hello.sh

因为，断行符`^M`少了，所以文件大小也变小了。

同理：可以使用`unix2dos`命令来将文件转换为Windows格式断行。

---

###小结

现在，我们知道在Linux下与Windows下的断行格式有冲突，因此，不建议在Windows系统当中将文件编辑好后传到Linux，会发生错误问题。不过，当在不同的系统之间进行文件的复制时，千万记得要使用unix2dos或者dos2unix命令来转换下格式。
	
