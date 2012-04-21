---
layout: post
title: "Git学习笔记"
date: 2012-04-21 13:39
comments: true
categories: 
- Git
---

前一段时间在[Github](https://github.com/)上用[Octopress](https://github.com/imathis/octopress)搭建了博客，从此，就需要和**Git**不断打交道，虽然用到的Git命令不是很多。

刚好，这几天x64移植项目告一段落，有了点空闲时间，想想还是系统地去学习一下Git吧。

当然，学习Git，我也希望在今后的开发中能够用Git来管理自己的Code，结束之前那种最原始的、靠每天备份的笨方法。

关于Git的教程，网上有不少，感觉用的人也挺多的，所以一般的资料和问题解决方法基本通过Google都能够获得。

这里，关于Git的历史、原理等不会涉及太多，主要是从自身日常管理Code这个角度去谈谈如何使用Git管理代码，让自己先达到能够熟练Git这个目标。

<!--more-->

##起点##

我学习Git的起点是从阅读[Pro Git](http://progit.org/book/zh/)开始，感觉各种概念讲解的还是蛮清楚的，涉及Git的很多方面，是一份很不错的资料。

另外，[Git Magic](http://www-cs-students.stanford.edu/~blynn/gitmagic/intl/zh_tw/index.html)也值得参考一下。

还有一份[Git使用指南](http://files.cnblogs.com/phphuaibei/git%E6%90%AD%E5%BB%BA.pdf) 作为使用Git管理Code的入门资料也是很不错的。

##工具##

我学习和使用Git的平台是**Windows**，所以安装一下[msysGit](http://code.google.com/p/msysgit/)提供的安装文件即可，我安装的是[Git-1.7.7-preview20111014.exe](http://code.google.com/p/msysgit/downloads/list)，不过目前已经更新到Git 1.7.10了。

安装完，就可以通过命令行工具**Git Bash**来使用Git了，我使用Git大多数时候是通过命令行的方式，当然在Windows下也支持图形界面的方式来使用Git，使用**Git GUI**就可以了。

不过，在Git GUI里有些特殊的功能可能没有支持，需要这些功能还是要切换到Git Bash中。

该工具还集成到了Windows的资源管理器中，在文件夹上右键，可以点击**Git Bash Here**和**Git GUI Here**快速启动Git并自动切换到指定的文件夹，方便了不少。

但有时，不知怎么突然无法通过该方法启动Git，上Google查了一下，发现是与Git相关的注册表项被修改了，可能是我使用了注册表清理工具造成的吧？

知道了原因，修复的方法也很简单，将下面的内容复制到一个文本文件中，保存后，将文件的扩展名修改为.reg，双击注册即可。

{% codeblock %}
REGEDIT4

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_shell]
@="Git Ba&sh Here"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_shell\command]
@="wscript \"C:\\Program Files\\Git\\Git Bash.vbs\" %1"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_gui]
@="Git &GUI Here"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_gui\command]
@="\"C:\\Program Files\\Git\\bin\\wish.exe\" \"C:\\Program Files\\Git\\libexec\\git-core\\git-gui\" --working-dir %1"
{% endcodeblock %}

值得一提的是，如果开发工具是MS Virtual Studio的话，则有另外一个Git图形界面工具[Git Extension](http://code.google.com/p/gitextensions/)可以使用，安装之后会集成到**VS2005、VS2008、VS2010**中，当然脱离MS Virtual Studio也可以单独使用，其图形界面的功能比上述的Git GUI要强大不少；同时，也集成到Windows资源管理器中，可以通过在文件夹上右键进入。

为了更深入地了解和掌握Git，下面基本是采用Git命令才演示各种操作。

##Git基本概念##

Git作为一个版本控制软件，相比其他版本控制软件有什么不同呢？

- Git是一个**分布式**的版本控制系统，一般来讲，各个Git仓库没有主次之分；

- 大多数的操作可以在本地完成，事后方便时，再推送到中心服务器的仓库中；

- 采用**"直接记录快照，而非差异比较"**的版本控制策略，内部只关心**文件数据的整体是否发生改变，而不是文件内容的具体差异**（Git内部被实现为一种微型的文件系统）；

- Git工作时就是在**工作目录（工作树、work tree）、暂存区（索引、index file）、本地仓库**三者之间管理文件的变化情况，Git会监视工作目录中的文件变化(增加新文件，删除文件，修改文件等)，需要我们自己手动将变化的文件添加(git add)到暂存区中（这就是文件快照），然后再提交(git  commit)到本地仓库中；上述过程，涉及Git内部的三种对象：commit对象、tree对象和blob对象，blob对象会对应的文件快照中那些变化的文件内容，tree对象记录了文件快照中各个目录和文件的结构关系，从概念上讲，tree对象和blob对象组成了文件快照，commit对象则记录了这次要提交到本地仓库的文件快照，同时也会指向上次的commit对象，它也是Git内部进行版本控制的重点（Git内部会记录各个commit对象，并用HEAD来指示当前分支中最新的commit对象），很多重要的功能，如分支、版本回溯、Git仓库内部状态等都是在commit对象基础上实现的；上述的每一个对象都对应一个独一无二的ID，该ID是一个20个字节的哈希码，由SHA-1算法计算而来；Git能够通过ID的前几个字节就识别出对应的对象；

- Git的分支功能很强大，很灵活，切换速度非常快，并且实现成本很低，这也是Git比其他版本控制软件要受欢迎的原因之一；

- Git拥有丰富的、功能强大的命令，一个命令通过配置不同的选项可以实现不同的功能；要学好Git，关键就是要掌握这些命令，并灵活使用它们；

##Git常用命令解析##

Git中有许多命令，并且每种命令都有一些功能选项可被选择，因此，在不熟悉Git这些命令的时候，查阅这些命令的使用说明是不错的选择。

要查询Git命令，可以使用git help 命令或者git 命令 --help的方式，它会自动打开浏览器来查看。

###1. Git仓库创建

`git init`

在项目的开始，必须使用该命令来创建和初始化Git仓库，它会在项目的文件夹下生成一个隐藏的**.git文件夹**，这就是这个项目的Git本地仓库，后面所有的Git命令操作都是针对该文件夹里的内容。执行过该命令后，原来的文件夹就成为了Git的工作目录。

*Note：在一个已经初始化过的文件夹下再次执行`git init`，Git并不会将之前的.git文件夹的内容清除，这应该是Git的一种保护。*

.git文件夹的初始组成如下：
{% codeblock %}
.git
   |
   |--HEAD
   |--description
   |--config
   |--[refs]
   |----|
   |----|--[heads]
   |----|--[tags]
   |--[objects]
   |----|
   |----|--[info]
   |----|--[pack]
   |--[info]
   |----|
   |----|--exclude
   |--[hooks]
   |----|
   |----|--*.sample
{% endcodeblock %}

初始状态下，Git默认处于master分支，HEAD文件的内容为ref: refs/heads/master，但在refs/heads目录下却没有master文件；而objects文件夹下则没有文件。

在有过一次提交后，.git文件夹就会产生变化，如增加了logs文件夹，里面记录了git各种操作产生的log，我们通过git命令，如git log, git show, git reflog等可以查看这些log内容；
产生了一个index文件，这就是暂存区对应的文件; objects文件夹下新增了很多文件夹和文件，它们实际就是文件快照（tree对象和blob对象）存放的地方；
refs/heads文件夹下这时生成了master文件，其内容就是master分支最新commit对象对应的ID；如果有其他分支，则在refs/heads文件夹下也会生成以分支名命名的文件，里面存储着该分支最新commit对象的ID。

###2. 文件快照

`git add .`

在Git中，工作目录下文件的状态可以分为已跟踪和未跟踪两大类状态，其中，已跟踪的文件是指已经被提交到git仓库的那些文件，而未跟踪的文件是指还没被提交到Git仓库中的那些非Git忽略的文件（Git可以通过在项目根目录下产生一个.gitignore文件，在里面指定要忽略的文件类型，这样Git就不会去监视这些文件的变化），如果工作目录中已跟踪的文件被修改或者删除，或者有新的文件（包括非空文件夹）加入，则通过`git status`可以查看到Git监视到文件变化情况，然后通过`git add .`做一次文件快照，并将其存储到暂存区（index文件）中，等待被提交到Git仓库中。

###3. 文件快照提交至仓库

`git commit -m '本次提交文件变化的描述信息'`

如果工作目录中的文件变化已经被暂存（也可以同`git status`来查看），则说明这次的文件快照可以被提交到仓库中，并一直保存。

提交时，需要添加一些信息，这里最好要将这次的文件变化情况描述清楚，以便以后在版本回溯时能够了解到各版本之间的差别。

如果工作目录中仅是已跟踪的文件被修改或被删除，则可以不用先`git add .`，直接使用`git commit -am "描述信息"`即可。

###4. 查看工作目录文件状态

`git status`

在git命令执行后，要养成通过`git status`查看git状态的习惯，以便及时了解文件变化的情况。通过`git status`可以知道文件的状态（已修改未暂存、已删除、已修改并已暂存等待提交、未跟踪）。

###5. 查看提交历史

`git log`

通过`git log`可以查看当前分支的所有提交历史，知道每次提交的commit对象的ID以及提交时附加的描述信息等。要显示更多的信息，需要使用其支持的选项，如`git log -p`可以将每次提交的文件变化也显示出来。

*Note ： `git log`显示的内容可能会比较多，但git bash上显示不下时，最下面会有一个冒号：，指示还有更多的内容，这是通过上下箭头就可以选择内容进行查看，要退出按q键即可，要查看其他命令，按h键。*

###6. 查看指定的提交对象

`git show commit-id`

通过`git log`可以显示整个提交历史，而通过`git show commit-id`则可以查看指定的某次提交内存，当然`git show -all`也可以显示出提交历史，另外还可以格式化显示内容。具体请查看其help。

*Note : commit-id可以是commit对象对应的ID，也可以是HEAD，分支名，tag等。*

###7. 查看工作目录/暂存区/仓库之间的差异

- `git diff`是比较工作目录与暂存区的差异
- `git diff HEAD`则是比较工作目录与仓库中最近一次的提交间的差异
- `git diff --cached`比较了暂存区与仓库中最近一次的提交间的差异。

###8. 分支的创建、删除、切换、合并、查看

Git一个比较吸引人的功能就是其强大的分支和合并功能。初始状态下，Git默认的分支为master。

- 通过`git branch`可以查看目前Git仓库中已有的分支；
- 创建分支 ：`git branch 新分支名 [分支起点]`，没有分支起点的话，则默认在当前分支的最新的提交上创建分支
- 切换分支 ：`git checkout 分支名`
- 创建同时切换到新分支 ：`git checkout -b 新分支名  [分支起点]`
- 合并分支到master分支 ：`git checkout master`  `git merge 要被合并的分支名`，合并过程中如果发生冲突则需要自己手动解决冲突，然后再提交。有冲突时，Git会显示哪个文件有冲突，并在冲突的文件中加上特殊的标识符号，解决完冲突后，要手动去掉这些被添加的标识符号。如果冲突比较复杂的话，最好使用其他工具来协助，通过git mergetool来启动。冲突一般是在不同的分支上对同一文件的同一位置内容进行了改动，并已提交到仓库中，这样在合并的时候就会发生冲突。
- 删除已经被合并过的分支 ： `git branch -d 要删除的分支`名，如果分支没有被合并过，该命令会执行失败
- 删除分支，不管有没有被合并过 : `git branch -D 要删除的分支名`
- 用图形界面查看分支提交历史 ： `gitk`

**充分利用好分支，可以帮助我们进行很好的版本控制与管理，如何用好分支其实是门艺术。**

基于分支的版本控制模型有一篇文章进行了很好的阐述。

[A succeddful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

中文翻译：[Git分支管理是一门艺术](http://roclinux.cn/?p=2129)

{% img /images/blogImgs/201204/git-branch-all.png %}

###9. 标签的添加、删除、查看

标签可以在需要的地方，为某个提交对象创建别名，这样以后我们就可以通过标签来查看一些信息，创建分支等。

- 查看标签 ：`git tag`
- 创建简单的标签 ： `git tag 标签名`
- 创建附加信息的标签 ： `git tag -a 标签名 -m '附加信息'`
- 通过标签查看信息 ： `git show 标签名`
- 删除标签 ： `git tag -d 标签名`

###10. 与远程仓库的交互

Git相比其他版本控制软件的一个优点就是大多数的操作都可以在本地进行，而不用管远程的仓库，因为操作是在本地，且操作的数据也是在本地，所以执行的速度就会比较快。
在多人协作的项目中，就需要涉及与远程仓库交互的问题，主要是如何从远程仓库抓取最新数据合并到自己的本地分支上，将自己的最新成果分享给其他人或让别人审查等 。

- 查看本地已经添加的远程仓库 ： `git remote`仅显示已添加的远程仓库名，`git remote -v`可以一并查看远程仓库的地址
- 在本添加远程仓库 ： `git remote add 远程仓库名 远程仓库地址`
- 删除本地添加的远程仓库 ：`git remote rm 远程仓库名`
- 重命名远程仓库名 ： `git remote rename 原名 新名`
- 克隆远程仓库到本地 ： `git clone 远程仓库地址 [克隆到指定文件夹]`
- 从远程仓库抓取最新数据到本地但不与本地分支进行合并 ： `git fetch 远程仓库名`
- 从远程仓库抓取最新数据并自动与本地分支进行合并 ： `git pull 远程仓库名 本地要合并的分支名`
- 将本地仓库推送到远程仓库中 ：`git push 远程仓库名 本地分支名`
- 查看远程仓库信息 ： `git remote show 远程仓库名`
- 将标签推送到远程仓库 ： `git push 远程仓库名 标签名`, 默认Git是不会将标签推送到远程仓库的

###11. 查看所有分支的所有的commit和reset操作记录（包括已删除的commit记录）

通过`git reflog`可以帮助我们获得将工作目录恢复到某个状态所需的ID(可以用HEAD@{数字}来表示对应的ID)。

###12. 撤销操作和版本回溯

有时候，由于我们的误操作，产生了一些错误，我们发现后希望能够及时纠正这些因为误操作而产生的结果，将工作目录恢复到某个正常状态。

- 撤销文件暂存，但还没有提交的文件： `git checkout -- filename` 或`git reset HEAD` ,修改的文件会被恢复到上次提交时的状态，修改的内容会丢失
- 版本回溯 ： [方法1] 根据分支或者标签将工作目录恢复到指定版本 : `git checkout 分支名或标签名`；
[方法2] 先通过`git reflog`找到某个版本的commit-ID，然后用`git reset --hard commit-ID`将工作目录的文件恢复到指定的版本
- 恢复工作目录中被删除的文件 (文件之前被提交到仓库中)：`git checkout -- filename` 或 `git checkout -f` 或 `git ls-files -d | xargs git checkout --`

###13. 备份工作目录

`git stash` `git stash list` `git stash apply` `git stash pop` `git stash clear`

如果正在一个develop分支上正在开发新功能，但这时master分支(稳定版本)突然发现了bug，并需要及时修复，而develop分支此时的工作还没有完成，且不希望将之前的工作就这样提交到仓库中时，这时就可以用git stash来暂时保存这些状态到Git内部栈中，并用当前分支上一次的提交内容来恢复工作目录，然后切换到master分支进行bug修复工作，等修复完毕并提交到仓库上后，再使用`git stash apply [stash@{0}]`或者`git stash pop`将工作目录恢复到之前的状态，继续之前的工作。

同时也可以多次使用`git stash`将未提交的代码压入到Git栈中，但当多次使用'git stash'命令后，Git栈里将充满了未提交的代码，这时候到底要用哪个版本来恢复工作目录呢？`git stash list`命令可以将当前的Git栈信息打印出来，我们只需要将找到对应的版本号，例如使用`git stash apply stash@{1}`就可以用版本号为stash@{1}的内容来恢复工作目录。

当Git栈中所有的内容都被恢复后，可以使用`git stash clear`来将栈清空。

###14. 二分查找

`git bisect`

###15. 垃圾回收

`git gc`

###16. 将当前工作目录文件压缩归档（不包括.git目录）

`git archive --format=zip -o arch.zip HEAD` 或 `git arch --format zip head>arch.zip`

###17. 跟上游分支同步

`git rebase 上游分支名`

假设master和develop是一个项目的两个分支，其中master是主分支，develop是从master而来的开发分支，如果在develop分支上提交过2次，之后又切换到master分支，做了一些修改并提交2次，这时，如果想将master分支的最新修改内容合并到develop分支，但同时也不能影响master分支时，就需要使用git rebase了，这时的上游分支为master。

{% codeblock %}
执行git rebase master前：
              develop: 1 --> develop: 2
            /
master: 0 --> master: 1 --> master: 2

执行git rebase master后：
                                    develop: 1 --> develop: 2
                                   /
master: 0 --> master: 1 --> master: 2
{% endcodeblock %}

###18. 查看commit的次数

`git shortlog -s -n`会显示出总的提交次数。

###19. 查看仓库中commit对象、tree对象和blob对象

在我们将文件提交到Git仓库后，我们可以通过每次的commit对象的ID来查看文件快照的内容。
具体的方法就是：
- 先通过`git log`查看提交历史，选择需要查看的commit-id
- `git cat-file -t id`可以知道拥有该ID的对象是属于哪种类型：**commit、tree、blob**
- `git cat-file commit id`可以查看到该commit对象指向的tree对象的ID
- `git ls-tree tree-id`可以查看该tree中的blob对象的ID和其他tree对象的ID（如果有）
- `git cat-file blob blob-id`


###20. 查看仓库中index文件 

通过`git ls-files --stage`可以查看当前分支的index文件中有哪些文件，它列出了文件名及对应的blob对象的ID。

###21. 查看仓库目录结构

`find`可以列出.git目录下所有目录和文件，这样就可以清楚地知道当前仓库的目录结构。

###22. 查看文件的修改历史

`git blame filename`可以列出该文件每次被修改的时间和内容。


###23. 常用的linux命令

- 创建文件夹 `mkdir`
- 删除文件夹 `rmdir`
- 查看文件列表 `ls`
- 查看文件内容 `cat`
- 回显 和 管道命令 `echo "hello" >> file.txt`


上述的这些命令应该能够帮助我们实现多数的版本控制需求，当然其中的每一个命令都会有一些其他的选项功能这里没有提到，希望在以后使用Git的过程中能够慢慢发掘，感受Git的强大！

