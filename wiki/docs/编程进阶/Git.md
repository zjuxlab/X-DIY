# Git

> 阅读完本篇文章后，你将不必面临小组大作业传压缩包的痛苦

[TOC]

## 版本控制

版本控制（Revision control）是一种在开发的过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录，备份以便恢复以前的版本的软件工程技术。

说白了就是用于管理多人协同开发项目的技术。

它到底有多重要？在你们写小组C大程大作业的时候自然就会体会到，不用我多讲。

## Git的历史

感兴趣的可以来[这里](https://www.jianshu.com/p/8821e9846773)看一眼。

## Git的安装配置

### 安装

[这篇文章](https://blog.csdn.net/weixin_44778232/article/details/127597405)介绍很详细，不再赘述。

### 用户配置

首先，也许你需要注册一个[GitHub](https://github.com)账号，这对你以后的学习和工作很有用。

在你有了自己的账号之后，有两种方式绑定你的账号——全局和非全局

先来介绍全局，它会:

```shell
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

非全局就是去掉`--global`，它只会在你当前的项目中生效，会保存在当前项目的 .git/config 文件里。

查看配置信息：

```shell
git config --list
```

1. ## git的理论基础

- 工作区域：
  - **工作区**：平时存放项目代码的地方。
  - **暂存区(Stage/Index)：**暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息
  - **版本库：**又称本地仓库，这个不算工作区，而是 Git 的版本库，里面有你提交到所有版本的数据。
  - **远程仓库**：托管代码的服务器

![img](https://tannin-1316822731.cos.ap-nanjing.myqcloud.com/asynccode)

- 工作流程：

  - 在工作目录中添加、修改文件；
  - 将需要进行版本管理的文件放入暂存区域；
  - 将暂存区域的文件提交到git仓库。
  -  因此，git管理的文件有三种状态：已修改（modified）,已暂存（staged）,已提交(committed)

  ![img](https://tannin-1316822731.cos.ap-nanjing.myqcloud.com/46875818-b19e-4fc2-bece-88082e454ce4.png)

## Git的基本使用（以GitHub为例）

* 新建一个仓库

  ![0114914dbd713c881d75826156ce36d](https://tannin-1316822731.cos.ap-nanjing.myqcloud.com/0114914dbd713c881d75826156ce36d.png)

* 在本地`cd`到你要上传到仓库的文件夹

* `git init`并配置用户信息（如果你不是全局配置的话）

* 链接远程仓库

```shell
git remote add origin http://xlab.zju.edu.cn/git/zzy/demo.git
```

- 将主分支重命名为main

```shell
git branch -M main
```

- 将本地所有代码加入暂存区

```shell
git add .
```

- 将暂存区的代码提交

```shell
git commit -m"提交时要说的备注"
```

- 将远程仓库同步到本地

```shell
git pull --rebase origin main
```

- 将提交push到远程仓库

```shell
git push origin main
```

## 分支管理——让你的代码协作高效起来的核心

- git分支：可以理解为多线程的开发，每个线程都在走，但彼此之间互不影响
- 如何做分支管理：

每一名成员单独使用一个分支进行开发，master（main）主分支应该非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下在各自的分支上工作，工作完后，代码稳定了，再合并到主分支上来。

- 分支管理的常用操作：

```shell
# 列出所有本地分支
git branch
# 列出所有远程分支
git branch -r
# 新建一个分支，但依然停留在当前分支
git branch [branch-name]
# 新建一个分支，并切换到该分支
git checkout -b [branch]
# 合并指定分支到当前分支
git merge [branch]
# 删除分支
git branch -d [branch-name]
# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

>  如果同一个文件在合并分支时都被修改了则会**引起冲突**：
>
> 1. 把本地冲突文件另保存起来
> 2. 还原本地文件版本。
> 3. 修改冲突文件后重新提交！

如果你能说服你的组员都使用git并做了合理的分支管理，那你们的合作开发将会是一件非常幸福的事情。

少传压缩包！！！少传压缩包！！！少传压缩包！！！