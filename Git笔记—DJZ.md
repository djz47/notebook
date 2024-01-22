# Git笔记—DJZ

## 简介

### 安装

```
sudo apt-get install git
```

### 创建版本库

创建版本库首先创建空文件夹，然后再在文件夹中进行初始化创建git仓库

```shell
$ mkdir git_train #创建文件夹
$ git init	#创建仓库
```

### 向仓库中添加文件

向仓库中添加文件主要分为两步，`add`和`commit`

```shell
$ git add readme.txt #add文件
$ git commit -m "wrote a readme file" #commit文件，'-m'后是解释性的语言
```

## Git中的常用操作

### 查看文件的修改状态

例如，之前已经将一个`readme.txt`文件加入了仓库，现在不知道该文件是否经过了修改，或者知道已经修改想要知道修改了哪些部分

```shell
$ git status #可以知道是否经过修改
$ git diff readme.txt  #可以知道修改了什么地方
```

### 版本回退

通过使用git，可以使文件恢复到某次修改后的版本

例如有三个版本的`readme.txt`文件，分别为：

版本1： wrote a readme file

```
Git is a version control system.
Git is free software.
```

版本2： add distributed

```
Git is a distributed version control system.
Git is free software.
```

版本3： append GPL

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

可以用`git log`指令得到各版本的信息

```shell
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```shell
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

Git提供了一个命令`git reflog`用来记录你的每一次命令：

```shell
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

回到`reset`前的版本可以通过`reset --hard 版本号`得到

```shell
$ git reset --hard 1094a
```

### 管理修改

#### 取消工作区的修改

当在工作区进行了修改，但是还未提交到暂存区时，`git checkout -- file`可以丢弃工作区的修改：

```shell
$ git checkout -- readme.txt
```

#### 取消暂存区的修改

用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉，重新放回工作区：

```shell
$ git reset HEAD readme.txt
```

### 删除文件

从工作区删除文件，直接`rm`

```shell
$ rm test.txt
```

从版本库中删除文件，`git rm`

```shell
$ git rm test.txt
```

## 远程仓库

一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。github就是一个远程仓库。

### [本地仓库通过SSH远程连接github](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名；

在连接过程中，遇到了22端口无法访问的问题，可参考[博客](https://segmentfault.com/a/1190000040896781)

连接成功之后，有更新只需要运行下面的命令即可完成同步

```shell
$ git push origin master
```

### 解绑远程库

首先使用`git remote -v`命令查看远程库信息

```shell
$ git remote -v
origin	git@github.com:djz47/train.git (fetch)
origin	git@github.com:djz47/train.git (push)
```

然后用`git remote rm <name>`解绑远程库

```shell
$ git remote rm origin
```

### 克隆远程库

把远程库克隆到本地

```shell
$ git clone git@github.com:djz47/train.git
```

## 分支管理

### 分支创建

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`或者`git switch <name>`

创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

### 冲突解决

使用`git status` 的指令会指出冲突的文件

```shell
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

使用`git log`可以看到分支的合并情况

```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   cf810e4 (HEAD -> master) conflict fixed
|\  
| * 14096d0 (feature1) AND simple
* | 5dc6824 & simple
|/  
* b17d20e branch test
* d46f35e (origin/master) remove test.txt
* b84166e add test.txt
* 519219b git tracks changes
* e43a48b understand how stage works
* 1094adb append GPL
* e475afc add distributed
* eaadf4e wrote a readme file
```

### 多人合作的工作模式

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

## 标签管理

默认标签是打在最新提交的commit上的，切换到需要打标签的分支上：

```shell
$ git branch
* dev
  master
$ git checkout master
Switched to branch 'master'
```

然后，敲命令`git tag <name>`就可以打一个新标签：

```shell
$ git tag v1.0
```

```shell
$ git tag -a <tagname>/commit id -m "blablabla..."
```

删除操作：

```shell
$ git tag -d v0.1
```

推送某个标签到进程：

```shell
$ git push origin <tagname>
```

命令`git push origin --tags`可以推送全部未推送过的本地标签；

命令`git tag -d <tagname>`可以删除一个本地标签；

命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。