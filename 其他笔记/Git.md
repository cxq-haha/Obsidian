Git官方教程：<a>https://git-scm.com/book/zh/v2</a>

## 概念
### Git的四个工作区域
* Workspace： **工作区**，平时存放项目代码的地方
* Index / Stage： **暂存区**，暂存区的作用是暂时存储被修改的文件。暂存区的代码有以下两个来源：
  1、idea新建一个文件，显示为红色，使用git add命令，将其加入到暂存区
  2、修改已有的文件，文件显示为蓝色
* Repository： **仓库区**（或版本库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本
* Remote： **远程仓库**，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换
![[Pasted image 20221025230404.png]]

### 文件的四种状态
![[Pasted image 20221025230741.png]]
**Untracked**:   未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.
**Unmodify**:   文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified.如果使用git rm移出版本库, 则成为Untracked文件
**Modified**: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过,返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改
**Staged**: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存,文件状态为Modified

![[Pasted image 20221025230958.png]]

新建文件--->Untracked
使用add命令将新建的文件加入到暂存区--->Staged
使用commit命令将暂存区的文件提交到本地仓库--->Unmodified
如果对Unmodified状态的文件进行修改---> modified
如果对Unmodified状态的文件进行remove操作--->Untracked

## Git常用命令

### 新建代码库
```shell
# 在当前目录新建一个Git代码库
git init
# 新建一个目录，将其初始化为Git代码库
git init [project-name]
# 下载一个项目和它的整个代码历史
git clone [url]
```

### 查看文件状态
```shell
#查看指定文件状态
git status [filename]
#查看所有文件状态
git status
```

### 工作区<-->暂存区
```shell
# 添加指定文件到暂存区
git add [file1] [file2] ...
# 添加指定目录到暂存区，包括子目录
git add [dir]
# 添加当前目录的所有文件到暂存区
git add .
#当我们需要删除暂存区或分支上的文件, 同时工作区也不需要这个文件了, 可以使用（⚠️）
git rm file_path
#当我们需要删除暂存区或分支上的文件, 但本地又需要使用, 这个时候直接push那边这个文件就没有，如果push之前重新add那么还是会有。
git rm --cached file_path
#直接加文件名   从暂存区将文件恢复到工作区，如果工作区已经有该文件，则会选择覆盖
#加了【分支名】 +文件名  则表示从分支名为所写的分支名中拉取文件 并覆盖工作区里的文件
git checkout
```

### 工作区<-->资源库（版本库）
```shell
#将暂存区-->资源库（版本库）
git commit -m '该次提交说明'
#如果出现:将不必要的文件commit 或者 上次提交觉得是错的  或者 不想改变暂存区内容，只是想调整提交的信息
#移除不必要的添加到暂存区的文件
git reset HEAD 文件名
#去掉上一次的提交（会直接变成add之前状态）   
git reset HEAD^ 
#去掉上一次的提交（变成add之后，commit之前状态） 
git reset --soft  HEAD^ 
```

### 远程操作
```shell
# 取回远程仓库的变化，并与本地分支合并
git pull
# 上传本地指定分支到远程仓库
git push
```

### 分支管理
```shell
# 创建分支
git branch (branchname)

# 查看分支
git branch -r  # 查看所有远程分支
git branch -a  # 查看所有本地分支和远程分支
git branch -vv  #查看远程分支详细信息
git branch -vva  #查看本地分支和远程分支详细信息

# 删除分支
git branch -d (branchname)

# 切换分支
git checkout (branchname)

# 合并分支
git meger branch1  # 将分支meger内容合并到当前分支
```

### 其它命令
```shell
# 显示当前的Git配置
git config --list
# 编辑Git配置文件
git config -e [--global]
#初次commit之前，需要配置用户邮箱及用户名，使用以下命令：
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
#调出Git的帮助文档
git --help
#查看某个具体命令的帮助文档
git +命令 --help
#查看git的版本
git --version
```


## 问题列表
### 在代码写到一半，需要pull主干最新的代码怎么办？

如果不想保存代码，可以将分支代码回退到上一个版本，执行pull。

如果想保存代码，将临时代码存储起来。执行pull命令后，再次恢复。操作如下：

1.  将代码先保存到暂存区，然后进行存储。之后重新创建分支。
   不推荐暂存区存在代码切换到主干。那么主干上也将存在暂存区的代码，分支再次切换主干时，可能会导致暂存区代码冲突。
   
```shell
git add . //现在在dev分支，代码添加文件到暂存区，但是没有commit
git stash //把代码暂存起来
git checkout master //切回主干
git checkout -b EmergencyIssue //创建分支修改EmergencyIssue
```

2.  开始在分支上修复bug，修复完毕，再次切会到dev分支。

```shell
git checkout master //修改好之后切回主线
git merge --no-ff -m "merged bug fix EmergencyIssue" iEmergencyIssue //分支合并
git branch -d EmergencyIssue //删除分支
git checkout dev //切回dev的分支
```

3.  切回到dev分支后，将事先存储的代码恢复。

```shell
git status //查看状态，但是发现什么都没有
git stash list //可以看到暂存区存起来的list
git stash pop //恢复stash后并删除stash的内容
```

IDEA中怎么操作：
1.  右击项目，选择Git，对暂存区文件进行临时存储。
 ![[Pasted image 20221025232012.png]]
 2.  填入临时存储的名称。
![[Pasted image 20221025232041.png]]
3.  完成分支的pull或者merge。
此处以`merge`命令举例。idea的路径是`VCS->Git`
![[Pasted image 20221025232058.png]]
4.  临时代码的恢复。  
因为在存储临时代码时，填写了message，恢复时，可以恢复特定的存储代码。
![[Pasted image 20221025232135.png]]
