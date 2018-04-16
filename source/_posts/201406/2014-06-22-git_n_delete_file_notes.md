---
layout : post
title : "版本库删除文件操作"
category : Git
tags : Git
date: 2014-06-22
---
首先，我们要知道在Git中任何的操作都是一个修改，那删除文件肯定也是一个修改操作了，在工作区删除一个文件可以使用`rm -f file`的命令来操作：

    $ rm -f one.txt
    
此时，Git已经知道你删除了文件，这时候的工作区和版本库也不一样了，可以使用`git status`来查看：

    hanson@host-linux:~/learngit$ git status 
    # On branch master
    # Changes not staged for commit:
    #   (use "git add/rm <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #   deleted:    one.txt
    #
    no changes added to commit (use "git add" and/or "git commit -a")


<!--more-->


现在有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且commit：

    hanson@host-linux:~/learngit$ git rm one.txt
    rm 'one.txt'
    hanson@host-linux:~/learngit$ git commit  -m 'delete file'
    [master 3ec942f] delete file
     1 file changed, 2 deletions(-)
     delete mode 100644 one.txt

现在，我们就彻底删除one.txt文件了。

另一种情况是我们失手删错了，因为我们版本库里还有该文件，我们可以使用`git checkout --file`来恢复：

    $ git checkout -- one.txt

git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

---
###小结
命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。I
