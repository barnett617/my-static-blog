---
title: git学习总结
date: 2017-11-17 09:54:33
lastmod: 2017-11-17 09:54:33
categories: 开发
tags:
  - git
---

对于 Git 学习的一些整理，包含常用命令整理

<!--more-->

个人觉得，对于一些开源工具，最好的学习资源还是其官网，我们就先来分析一波 Git 究竟是做什么的。

### 概念解析

git 官网的定义是：Git 是一种免费并且开源的分布式版本控制系统，被设计用来快速高效地处理堆积成大工程的每一块小部分。（个人翻译，不喜勿喷）

Git 简单易用、占用空间小并且性能优越。它远超过一些伴有类似廉价的本地分支、方便的阶段区域和多工作流特点的配置管理工具（SCM，Software Configuration Management），比如 SVN、CVS、Perforce 和 ClearCase 这些。

Git 允许同一组下的人们同一时刻在相同的文档上工作（通常是代码），并且不会踩到其他人的脚趾（形容两个人同时在相同的文档上工作也不会发生冲突）

### 特性

- 小而快速（Small and Fast）

- 分布式（Distributed,这也是它比 SVN 优势明显的地方）

- 数据保证（Data Assurance）

- 分阶段区域（Staging Area）

- 免费并开源（Free and Open Source）

### 教程

初级教程：<a href="https://try.github.io/levels/1/challenges/1">Try Git</a>

高级教程：<a href="http://gitreal.codeschool.com/levels/1/challenges/1">gitreal</a>

官方书籍：<a href="https://git-scm.com/book/zh/v2">Pro Git</a>

### 使用步骤（包含常见命令）

1.初始化

```
git init
```

init 命令会创建一系列 git 结构的文件

仅对一个制定目录创建 git 结构时使用，用于将某个目录交给 git 管理

2.查看 git 状态

```
git status
```

可在任何时间阶段使用， 以查看当前 git 管理下的文件状态

3.将文件交予 git 管理

```
git add filename
```

对于在 git init 后的目录里的每一个文件都有 tracked 和 untracked 两种状态，也就是**是否被 git 追溯（管理）**

对于每一个文件，要么通过 git add 交由 git 管理，要么加入 ignore 行列，不然新创建的文件就会处于不受 git 管理的“游离”状态。

当对于新创建的文件操作 git add 之后，文件就会处于 Staging Area（阶段区域），在 commit 之前还尚未放进 git 的仓库（repository），所以在文件到仓库之前，我们可以将文件添加（add）进或是移出 Staging Area（暂存区）

当然我们也可以对文件进行批量添加到暂存区，比如 git add '\*.txt'来将所有后缀为 txt 的文件添加进暂存区（注意引号）

4.将暂存区的文件提交到仓库

```
git commit -m "提交注释"
```

5.查看提交历史

```
git log
```

6.将本地仓库与远端仓库建立关联
为了将本地仓库推到远端 Github 服务器中（Push local Repo to Github Server），我们需要添加一个远端仓库（add a remote repository）

```
git remote add origin `Remote Repository URL`
```

这条命令初学者看起来晦涩难懂，我也是研究了很多遍才大致理解，首先这与普通的 git add 不同的是，git add 是将本地新建的文件添加到 git 的暂存区，而 git remote add 是表示添加一个远端仓库，与本地的仓库建立关联。

默认取名 origin，直观易懂，后面的 URL 也就是远端仓库的地址，虽然在远端（Github）创建仓库的时候我们创建了名字，但现在毕竟本地还没有那个仓库，所以相当于先在本地创建一个空名字，以备和远端的实体仓库对应。

> 当你使用 git remote 命令时，可以看到你 add 的“虚拟”远端仓库，它们的地址就是你 add 时最后的 URL

7.将本地仓库内容推到远端

```
git push -u origin master
```

我们表示远端仓库的代号是 origin，默认的本地分支名是 master，-u 是告诉 git 去记住这两个参数（origin master），这样下次提交就可以简化为 git push

---

经过上面的 7 个步骤，我们就完成了在本地创建 git 仓库，并创建文件交由 git 管理，并将本地仓库与远端仓库进行关联，再将本地仓库的文件上推到远端仓库

---

接下来是关于远端发生变更，本地来查看（比如其他人 fork 了你的仓库，并且 commit 了内容，并 pull request）

8.将远端仓库的最新文件拉到本地

```
git pull origin master
```

看懂 git push origin master 再来看这条命令就好理解一些，git pull 是拉（下载）代码，也就是从远端往本地拉，然而其实本地的 origin 其实就是一个关联了远端仓库的钩子，你从 origin 拉代码就是从远端仓库拉代码，而拉下来的代码要放在哪个分支，这里选择了 master 主干分支

9.对比拉下来的远端仓库最新代码和本地的区别

```
git diff HEAD
```

HEAD 是一个指针，指向我们最新 commit 的地方，不加 HEAD，默认也是与 HEAD 对比

#### 其他命令

10.使用 diff 命令对比暂存区文件之间的区别

```
git diff --staged
```

前面提到过 git add 命令是将文件添加入暂存区，即 add to stage

11.回撤 add 行为

```
git reset
```

我们可以通过 git add 将文件交由 git 管理，同样也可以通过 git reset 将文件脱离 git 管理，即移出暂存区（unstage file）。
当执行完 git reset somefile 后，somefile 会脱离 git 管理，但文件依旧会在那里。

12.Undo

```
git checkout -- somefile（注意破折号与文件名之间有一个空格，否则会提示“未定义的选项‘--文件名’”）
```

可以通过 git checkout 命令将文件恢复成它们上一次提交前的样子

13.创建分支（branch out）

```
git branch branchname
```

当需要在主分支之外开发新功能或者修改 bug，需要拉另外的分支以与主分支分离，来进行提交，当工作完成后可以再将其分支合并回主分支（merge back to master）
当直接输入 git branch 命令是查看当前分支情况，当 git branch 后面加任意名字即为创建新的分支

14.切换分支

```
git checkout branchname
```

15.清除文件

```
git rm filename
```

git rm 命令不仅会删除你本地的实体文件，还会同时将这些删除行为追溯到 git（stage these removal）
删除时同样可以使用通配符（wildcard）来一次清扫多个文件，例如 git rm '\*.txt'
当然这些删除行为也需要通过 commit 来通知仓库，尽管是删除文件，但对于 git 来说都是新增一次操作
此时在新建的分支，将所有的文件删除并提交，仅是对本分支的操作，当切换到主分支时，主分支依然是原样（分支相当于对主分支的拷贝，然而实际实现并不是通过拷贝实现）

16.合并分支

```
git merge branchname
```

git merge 是将输入的分支合并到当前分支，一般是切回到主分支，执行合并命令，来将其他某一个分支合并到主分支

17.删除分支

```
git branch -d branchname
```

当分支合并回主分支后，就没用了，可以进行删除

### 使用场景

1.先在本地通过 git 创建工程，然后上传到远程新建的空仓库(Github)

2.在远端仓库（Github）创建文件或工程，克隆到本地，进行修改后再提交到远端仓库

3.fork 其他人的仓库，若符合工程结构，则下载到本地可直接运行，修改，提交

参考链接：

- <a href="https://git-scm.com/">git 官网</a>
