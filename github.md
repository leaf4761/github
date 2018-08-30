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

Git跟踪并管理的是修改，而非文件

第一次修改 -> git add -> 第二次修改 -> git commit

Git管理的是修改，当用git add命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，git commit只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

## 撤销修改

1. 还未执行git add命令，git checkout -- file可以丢弃工作区的修改，把file文件在工作区的修改全部撤销，这里有两种情况：

    一种是file自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

    一种是file已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态；

    总之，就是让这个文件回到最近一次git commit或git add时的状态。

        $ git checkout -- readme.txt
        
2. 已通过git add将文件放到暂存区了，用命令git reset HEAD `<file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区

        $ git reset HEAD readme.txt
        Unstaged changes after reset:
        M    readme.txt

    git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当使用HEAD时，表示最新的版本。

3. 已通过git commit命令提交到了版本库，使用git reset --hard/sorft commit_id命令

        $ git reset --hard HEAD^
        $ git reset --hard 1094a

## 删除文件

当文件在工作区删除后，有以下两个选择：

1. 确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit，将文件从版本库中删除

        $ git rm test.txt
        rm 'test.txt'

        $ git commit -m "remove test.txt"
        [master d46f35e] remove test.txt
        1 file changed, 1 deletion(-)
        delete mode 100644 test.txt

2. 删错了，因为版本库里还有，所以可以很轻松地把误删的文件恢复到最新版本

        $ git checkout -- test.txt

# 远程仓库

[Github](https://github.com/, "github")提供Git仓库托管服务。要使用github，需要注册一个账号，然后进行如下设置：

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

    $ ssh-keygen -t rsa -C "youremail@example.com"
    一路回车。.ssh目录下会生成id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，id_rsa.pub是公钥

第2步：登陆GitHub，打开Account settings->SSH Keys页面，点击Add SSH Key，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容。只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

第3步：添加远程仓库。登陆GitHub，点击右上角Create a new repo按钮，填入相关信息，不勾选Initialize this repository with a README，创建空的远程仓库。创建完成后可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，把本地仓库的内容推送到GitHub仓库。

    远程库的名字（localRepositoryName）git默认是origin
    $ git remote add <localRepositoryName> git@github.com:<username>/<repository.git>
    推送当前master分支的内容
    $ git push -u <localRepositoryName> master

由于远程库是空的，第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令：

    $ git push <localRepositoryName> master

类似的，也可从远程库克隆。创建一个远程仓库，勾选Initialize this repository with a README，用命令git clone克隆一个本地库：

    $ git clone git@github.com:<username>/<repository.git>

GitHub给出的地址不止一个，还可以用https://github.com/username/repository.git这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

# 分支管理

# 小结

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：
1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令`git commit -m <message>`，完成。

要随时掌握工作区的状态，使用git status命令。

如果git status显示有文件被修改过，用`git diff <filename>/git diff HEAD -- <filename>`可以查看修改内容。

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard/soft commit_id`。

用git log可以查看提交历史，以便确定要回退到哪个版本。

用git reflog查看命令历史，以便确定要回到未来的哪个版本。

场景1：当改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，使用命令`git reset --hard/soft commit_id`，不过前提是没有推送到远程库。

命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么永远不用担心误删，但是只能恢复文件到最新版本，会丢失最近一次提交后修改的内容。

要关联一个远程库，使用命令`git remote add localRepositoryName git@server-name:path/repo-name.git`；

关联后，使用命令`git push -u localRepositoryName master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone git@github.com:<username>/<repository.git>`命令克隆。

Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。