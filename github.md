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
    让Git显示颜色，会让命令输出看起来更醒目
    $ git config --global color.ui true
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

首先创建一个文本文件`readme.md`，放入git目录下：

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

    git reset –-soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可。
    git reset -–hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容，撤销的commit中所包含的更改被冲掉。

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

    一种是file自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态。

    一种是file已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

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

类似的，也可从远程库克隆。创建一个远程仓库，勾选Initialize this repository with a README，用命令git clone克隆一个本地库（若使用ssh方式，需要把SSH Key添加到GitHub）：

    $ git clone git@github.com:<username>/<repository.git>

GitHub给出的地址不止一个，还可以用https://github.com/username/repository.git这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。

使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

## 分支管理

在git里，master分支为主分支，也是默认的分支。head严格来说是指向master的，master指向提交，即head指向当前分支。
每次提交，master分支都会向前移动一步。

当创建新的分支，例如dev时，Git新建了一个指针叫dev，指向与master相同的提交，再把HEAD指向dev。即当前分支为dev。从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变。

当在dev上的工作完成后，需要合并分支时，最简单的方法，就是直接把master指向dev的当前提交。合并完分支后，可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，就剩下了一条master分支。

创建dev分支，然后切换到dev分支：

    $ git checkout -b dev
    -b参数表示创建并切换，相当于以下两条命令:
    $ git branch dev
    $ git checkout dev

用git branch命令查看当前分支：

    $ git branch

如果在dev分支上正常提交，再切换回master，则在master上无法看到早dev上的修改，现在把dev分支的工作成果合并到master分支上（前提是已切换到master上）：

    $ git merge dev
    如果执行命令后出现Fast-forward字样，则git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快

合并完成后，就可以地删除dev分支了：

    $ git branch -d dev
    删除后，查看branch，就只剩下master分支了

## 解决冲突
当创建切换了一个新的分支后，并在上面做了修改，此时切换回master，同时在master上有做了不同的修改，就会产生冲突。吐过此时进行分支合并，则Git无法执行“快速合并”。执行`git merge branch_id`命令，Git告诉我们，文件存在冲突，必须手动解决冲突后再提交。

此时查看文件内容，Git会用<<<<<<<，=======，>>>>>>>标记出不同分支的内容。

手动修改后，保存提交，此时冲突解决，分支完成合并。可以通过git log命令查看。最后删除分支即可

    $ git log --graph --pretty=oneline --abbrev-commit

# 分支管理策略

通常情况下，git会用fast forward模式，但在这种模式下，删除分支后会同时丢弃分支信息。如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。可以通过--no-ff参数禁用fast forward方式。

    $ git merge --no-ff -m "merge with no-ff" dev

### 分支策略

1. 首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活。
2. 干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本。
3. 团队合作时，每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

## bug分支

每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

场景：当接到一个修复一个代号001的bug的任务时，很自然地，你想创建一个分支issue-001来修复它，但是，当前正在dev上进行的工作还没有提交，工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug。

Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

    $ git stash
    Saved working directory and index state WIP on dev: f52c633 add merge

执行stash命令后，用git status查看工作区，就是干净的（除非有没有被Git管理的文件）。此时可以放心创建分支了。

首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支:

    $ git checkout master
    $ git checkout -b issue-001

在修复bug，然后保存提交：

    $ git add readme.txt 
    $ git commit -m "fix bug 001"

修复完成后，切换到master分支，并完成合并，最后删除issue-001分支：

    $ git checkout master
    $ git merge --no-ff -m "merged bug fix 001" issue-001

解决完bug，可以切换会dev继续工作了：

    $ git checkout dev
    $ git status

工作区是干净的，如需要查看刚才的工作现场，用git stash list命令：

    $ git stash list

恢复方式有两种：

1. 用git stash apply恢复，但是恢复后，stash内容并不删除，需要用git stash drop来删除。
2. 用git stash pop，恢复的同时把stash内容也删了：

```
$ git stash pop
```

可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：

    $ git stash apply stash@{0}

## feature分支

添加一个新功能时，肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。步骤如下：

    $ git checkout -b feature-001
    $ git add feature.py
    $ git commit -m "add feature 001"
    切回dev，准备合并：
    $ git checkout dev

一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。
如果在此时，接到通知，需要取消此功能。执行命令：

    $ git branch -d feature-001
    error: The branch 'feature-001' is not fully merged.
    If you are sure you want to delete it, run 'git branch -D feature-001'.
    
只能执行强制删除命令，加上-D参数，现在我们强行删除：

    $ git branch -D feature-001

## 多人协作

当从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用`git remote`或`git remote -v`显示更详细的信息：

上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

    $ git push origin master
    或者推送到其他分支
    $ git push origin dev

并不是一定要把本地分支往远程推送，哪些需要推送呢？
1. master分支是主分支，因此要时刻与远程同步。
2. dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步。
3. bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug。
4. feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，看具体情况。

### 抓取分支

多人协作时，大家都会往master和dev分支上推送各自的修改。

模拟一个小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：

    $ git clone git@github.com:<username>/<repository.git>

当小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支：

    $ git branch

小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：

    $ git checkout -b dev origin/dev

现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：

    $ git add env.txt
    $ git commit -m "add env"
    $ git push origin dev

小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

    $ cat env.txt
    $ git add env.txt
    $ git commit -m "add new env"
    $ git push origin dev
    To github.com:<username>/<repository.git>
    ! [rejected]        dev -> dev (non-fast-forward)
    error: failed to push some refs to 'git@github.com:<username>/<repository.git>
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Integrate the remote changes (e.g.
    hint: 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

推送失败，因为小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：

    $ git pull
    There is no tracking information for the current branch.
    Please specify which branch you want to merge with.
    See git-pull(1) for details.

        git pull <remote> <branch>

    If you wish to set tracking information for this branch you can do so with:

        git branch --set-upstream-to=origin/<branch> dev

git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：

    $ git branch --set-upstream-to=origin/dev dev
    Branch 'dev' set up to track remote branch 'dev' from 'origin'.

增加链接后，git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push。

    $ git push origin dev

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用git push origin <branch-name>推送自己的修改。
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并。
3. 如果合并有冲突，则解决冲突，并在本地提交。
4. 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
5. 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

## rebase

多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先pull，在本地合并，然后才能push成功。多次合并再push后，git log查看会很乱。

Git有一种称为rebase的操作，有人把它翻译成“变基”。

rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

    $ git rebase

# 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。标签也是版本库的一个快照。

## 创建标签

1. 切换到需要打标签的分支上：

        $ git branch
        * dev
        master
        $ git checkout master
        Switched to branch 'master'
2. 命令git tag <name>就可以打一个新标签：

        $ git tag v1.0
3. 命令git tag查看所有标签：

        $ git tag
        v1.0
默认标签是打在最新提交的commit上的。如果需要打在以前提交的分支上：

    $ git log --pretty=oneline --abbrev-commit
    找到commit_id
    $ git tag v0.9 commit_id
    $ git tag
    v0.9
    v1.0
还可以创建带有说明的标签，用-a指定标签名，-m指定说明文字：

    $ git tag -a v0.1 -m "version 0.1 released" 1094adb
标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息，可以看到说明文字：

    $ git show v0.9

标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 

## 操作标签

删除标签：

    $ git tag -d v0.1

创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

要推送某个标签到远程，使用命令`git push origin <tagname>`：

    $ git push origin v1.0
    或者，一次性推送全部尚未推送到远程的本地标签：
    $ git push origin --tags

要删除远程标签就麻烦一点，先从本地删除：

    $ git tag -d v0.9
再从远程删除。删除命令也是push，但是格式如下：

    $ git push origin :refs/tags/v0.9

# 使用github参与开源项目

*以bootstrap为例*：

bootstrap的主页为：https://github.com/twbs/bootstrap

访问主页。点“Fork”就在自己的账号下克隆了一个bootstrap仓库，然后，从自己的账号下clone：

    git clone git@github.com:<username>/bootstrap.git

一定要从自己的账号下clone仓库，这样才能推送修改。如果从bootstrap的作者的仓库地址git@github.com:twbs/bootstrap.git克隆，因为没有权限，将不能推送修改。

如果想修复bootstrap的一个bug，或者新增一个功能，立刻就可以开始干活，干完后，往自己的仓库推送。

如果希望bootstrap的官方库能接受你的修改，你就可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了。

# 使用码云

使用GitHub时，国内的用户经常遇到的问题是访问速度太慢，有时候还会出现无法连接的情况。

如果希望体验Git飞一般的速度，可以使用国内的Git托管服务——[码云](https://gitee.com/ "码云")。

和GitHub相比，码云也提供免费的Git仓库。此外，还集成了代码质量检测、项目演示等功能。对于团队协作开发，码云还提供了项目管理、代码托管、文档管理的服务，5人以下小团队免费。

码云的免费版本也提供私有库功能，只是有5人的成员上限。 

码云的使用方式与GitHub类似，需要先上传ssh pub key，然后在码云上创建对应的资源库

然乎，在本地库上使用命令`git remote add`把它和码云的远程库关联：
    
    git remote add origin git@gitee.com:liaoxuefeng/learngit.git

如果在使用命令git remote add时报错：

    git remote add origin git@gitee.com:liaoxuefeng/learngit.git
    fatal: remote origin already exists

这说明本地库已经关联了一个名叫origin的远程库，此时，可以先用`git remote -v`查看远程库信息，可以删除已有的GitHub远程库：

    $ git remote rm origin

如果想一个库既关联github又关联码云，需要注意，git给远程库起的默认名称是origin，如果有多个远程库，我们需要用不同的名称来标识不同的远程库：

    git remote add github git@github.com:<username>/<repository.git>
    git remote add gitee git@gitee.com:<username>/<repository.git>
    git push github master
    git push gitee master

码云也同样提供了Pull request功能，可以让其他小伙伴参与到开源项目中来。

# 自定义git

git安装完成后，还有很多全局配置项。

## 忽略特殊文件

有些文件在工作空间中，但不需要提交它们，可以在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写.gitignore文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

忽略文件需要遵循以下原则：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
3. 忽略带有敏感信息的配置文件，比如存放口令的配置文件。

.gitignore示例：

    # Windows:
    Thumbs.db
    ehthumbs.db
    Desktop.ini

    # Python:
    *.py[cod]
    *.so
    *.egg
    *.egg-info
    dist
    build

    # My configurations:
    db.ini
    deploy_key_rsa
把.gitignore也提交到Git，就行了。

有些时候，想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了：

    $ git add App.class
    The following paths are ignored by one of your .gitignore files:
    App.class
    Use -f if you really want to add them.
可以通过-f命令强制提交：

    $ git add -f App.class

也可能是.gitignore写得有问题，可以用git check-ignore命令检查：

    $ git check-ignore -v App.class
    .gitignore:3:*.class    App.class

# 配置别名

例如为git status设置别名：

    $ git config --global alias.st status
    设置别名后，可以通过使用git st命令查看git状态了

--global参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

又如为git reset设置别名：

    $ git config --global alias.unstage 'reset HEAD'
    当你敲入命令：
    $ git unstage test.py
    实际上Git执行的是：
    $ git reset HEAD test.py

可以设置高亮git log：

    git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

## 配置文件

每个仓库的Git配置文件都放在.git/config文件中。

当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中。

配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

# 搭建git服务器

搭建Git服务器需要准备一台运行Linux的机器，在Ubuntu或Debian下，通过几条命令即可：
1. 安装git

        $ sudo apt-get install git
2. 创建一个git用户，用来运行git服务

        $ sudo adduser git
3. 创建证书登录

        收集所有需要登录的用户的公钥，就是他们自己的id\_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。
4. 初始化Git仓库

        先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
        $ sudo git init --bare sample.git
        把owner改为git
        $ sudo chown -R git:git sample.git
5. 禁用shell登陆

        第2步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。
        git:x:1001:1001:,,,:/home/git:/bin/bash
        改为：
        git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
6. 克隆远程仓库

        $ git clone git@server:/srv/sample.git

## 管理公钥

如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。如果团队有几百号人，可以用[Gitosis](gitosis)来管理公钥。

## 管理权限

Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。[Gitolite][gitlite]就是这个工具。

[gitlite]:https://github.com/sitaramc/gitolite "gitlite"
[gitosis]:https://github.com/res0nat0r/gitosis "Gitosis"

# 小结

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：
1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件。
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

要关联一个远程库，使用命令`git remote add localRepositoryName git@server-name:path/repo-name.git`。

关联后，使用命令`git push -u localRepositoryName master`第一次推送master分支的所有内容。

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone git@github.com:<username>/<repository.git>`命令克隆。

Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。

Git分支十分强大，在团队开发中应该充分应用。

合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除。

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再git stash pop，回到工作现场

开发一个新feature，最好新建一个分支。

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

查看远程库信息，使用`git remote -v`。

本地新建的分支如果不推送到远程，对其他人就是不可见的。

从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交。

在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致。

建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`。

从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

rebase操作可以把本地未push的分叉提交历史整理成直线。

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

命令`git tag <tagname>`用于新建一个标签，默认为HEAD，也可以指定一个commit id。

命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息。

命令`git tag`可以查看所有标签。

命令`git push origin <tagname>`可以推送一个本地标签。

命令`git push origin --tags`可以推送全部未推送过的本地标签。

命令`git tag -d <tagname>`可以删除一个本地标签。

命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

在GitHub上，可以任意Fork开源仓库。

自己拥有Fork后的仓库的读写权限。

可以推送pull request给官方仓库来贡献代码。

忽略某些文件时，需要编写.gitignore。

.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理。

给Git配置好别名，就可以输入命令时偷个懒。可以通过命令修改，也可以通过.git/config文件直接修改。

搭建Git服务器非常简单，通常10分钟即可完成。

要方便管理公钥，用[Gitosis][gitosis]。

要像SVN那样变态地控制权限，用[Gitolite][gitolite]。