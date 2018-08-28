# github安装
### *Linux*

1. Debian或Ubuntu 通过sudo apt-get install git安装

2. 其他版本源码进行安装

### *Mac os*

1. 安装homebrew，然后通过homebrew安装Git

2. 安装Xcode，Xcode集成了Git

### _windows_

安装git bash

安装完成后，设置用户名和邮箱

    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"
# 初识
## 创建版本库

版本库又名仓库，英文名repository，可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史。

创建版本库：

    $ mkdir git
    $ cd git
    $ git init
git仓库创建好之后，会在当前目录下多了一个.git目录

## 把文件添加到版本库

git只能跟踪文本文件的改动，二进制文件无法跟踪。最好文本为utf-8编码，windows不要使用记事本打开。

首先创建一个文本文件readme.md，放入git目录下：

    Git is a version control system.
    Git is free software.
通过git add 命令添加仓库。git commit提交，可以一次提交多个文件，-m后面输入的是本次提交的说明

    $ git add readme.txt
    $ git add file2.txt file3.txt
    $ git commit -m "wrote 3 files"

# git本地操作

## 运行git status命令查看当前仓库状态

    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

    no changes added to commit (use "git add" and/or "git commit -a")

## 通过git diff `<filename>` 比对文件内容

## 通过git log命令，查看每次提交的内容，加上--pretty=oneline可以让输出结果显示在一行里

    $ git log --pretty=oneline
    1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
    e475afc93c209a690c39c13a46716e8fa000c366 add distributed
    eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file

前面部分为版本号，通过SHA1计算出来

在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，往上100个版本可以写成HEAD~100。也可以使用版本号的前几位来表示某个版本

## 如果进行了错误的提交，但是还没有push到远程分支，想要撤销本次提交，可以使用git reset –-soft/hard命令，回退到某一个版本，可以使用git reset命令：

    $ git reset --hard HEAD^
    $ git reset --hard 1094a

二者区别：

    git reset –-soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可；
    git reset -–hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容，撤销的commit中所包含的更改被冲掉；

Git提供了一个命令git reflog用来记录每一次命令，包括回退head后的版本。

## 名词解释

* 工作区（Working Directory）：即实际工作的电脑中的目录文件
* 版本库（Repository）：工作区中的.git隐藏目录。版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。默认提交是提交到master分支上

工作区--(add)-->暂存库---(commit)--->master

## 管理修改

# 小结

初始化一个Git仓库，使用git init命令。

添加文件到Git仓库，分两步：
1. 使用命令git add `<file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令git commit -m `<message>`，完成。

要随时掌握工作区的状态，使用git status命令。

如果git status显示有文件被修改过，用git diff可以查看修改内容。

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard/soft commit_id。

用git log可以查看提交历史，以便确定要回退到哪个版本。

用git reflog查看命令历史，以便确定要回到未来的哪个版本。