---
title: Git
date: 2022-03-21 13:58:03
categories:
tags:
---

# git使用

## 目的：Linus为了管理Linux源码用C语言开发的分布式版本控制系统

## 类型：分布式

### 分布式 VS 集中式

1. 集中式：CVS和SVN等
2. 分布式：git等
3. 集中式通常也有一台充当中央服务器的电脑，主要是为了方便交换大家的修改

### 集中式的缺点

- 版本库集中存放在中央服务器，需要联网获得最新的版本
- 只有一个中央服务器可能会宕机，导致所有人无法工作
- 中央服务器的版本库一旦出现存在问题，难以补救，不安全

## let's start:)

### 准备

1. download git
2. 设置本机的user信息，本机所有repository都会使用这个配置，对单独的仓库也可以单独设置，通过git config命令查看参数选项

``` bash
$ git config --global user.name "your name"
$ git config --global user.email "email@example.com"
```

3. 查看本地global参数

``` bash
$ git config --list --global
```

<span style="color:red;font-weight:bold">!!! 修改global参数后以前的提交还是以前的user参数，???git repository如何识别user呢</span>

4. 初始化一个仓库，会在当前目录添加一个隐藏目录(.git)

``` bash
$ git init
```

<span style="color:red;font-weight:bold">!!! git 只能跟踪文本文件的改动，图片、视频这些二进制文件，没法跟踪文件的变化</span>

### changes not staged for commit

1. untracked changes -> staged changes(update what will be committed)

``` bash
$ git add <files>
```

2. discard changes in working directory(将工作区的状态回到暂存区的状态)

``` bash
$ git checkout -- <files>
```

### 要提交的变更

1. staged changes -> untracked changes(将暂存区会退到到head所指的分支，并把暂存区的修改回退到工作区)

```bash
$ git reset HEAD <files>
```

2. 提交

```bash
$ git commit -m ""
```

## 查看repository的状态

1. 查看过去日志

```bash
$ git log
```

2. 查看当前状态

```bash
$ git status
```

## 版本控制

1. 回到过去(之前的修改保存到untracked changes，暂存区也回退)

```bash
$ git reset HEAD^
$ git reset HEAD~1
```

2. 强制回退，可以通过commitID在回去，丢弃所有目前的修改，不建议用

```bash
$ git reset --hard HEAD^
$ git reset --hard 7f1f
```

3. 查看命令历史，回到未来

```bash
$ git reflog
```

## 管理修改

1. 比较工作区和HEAD指针所指向的分支

``` bash
$ git diff
```

## 远程库

```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```

1. 本地仓库关联远程仓库

```bash
# 测试一下ssh-key添加是否成功
$ ssh -T git@github.com
# 关联远程仓库，本地取名叫origin
$ git remote add origin https://github.com/love1angel/work.git
# 查看本地分支，即可查看远程分支remotes/origin/main
$ git branch -a
# 将本地分支master重命名为main分支
$ git branch -M main
# 第一次push main分支，远程库会创建main分支，同时将本地main分支push到orgin的main分支，-u参数会将本地的main分支与远程的main分支关联起来
$ git push -u origin main
# 直接push main分支到origin
$ git push origin main

# 远程库
$ git remote -v
# 删除远程库联系
$ git remote rm origin

# 使用clone会自动关联远程库
$ git clone
```

## 分支管理

## 创建合并分支

```bash
# 新建分支并切换
$ git checkout -b dev

# equals to
$ git branch dev
$ git checkout dev

# 检查一下
$ git branch

# 将dev分支合并到master分支
$ git merge dev

# 删除分支
$ git branch -d dev
```

## 冲突解决

```bash
# 合并分支时出现冲突，变化会保存当前分支
$ git merge fearture1

# 解决冲突后
$ git add
$ git commit

# 解决完成，合并成功，查看一下
$ git log --graph

## 禁用fast forward，fast forward是直接修改当前指针跳转，看不出来曾经做过合并，禁用会在当前分支新增一个commit
$ git merge --no-ff -m "merge with no-ff" dev
```

## BUG分支，feature分支

```bash
# 保存 Saved working directory and index state WIP
$ git stash

$ git stash list

# 恢复指定的
$ git stash apply stash@{0}
# 删除stash内容
$ git stash pop stash@{0}

# 在当前分支应用某个commit
$ git cherry-pick <commit>

```

## 多人开发

```bash
# 关联本地新建dev分支到远程的dev分支
$ git checkout -b dev origin/dev

# 有人提交后，与本地有冲突，需要先pull，在本地merge，解决冲突，在推送
$ git branch --set-upstream-to=origin/<branch-name> <branch-name>

# 本地两次提交，pull下来一次提高，将本地未push的分叉提交历史拉直
$ git rebase
```

## tag

tag就是某个commit，不过commitID不好记住，且无意义

```bash
$ git tag v1.0
$ git tag
$ git tag v0.9 f52c633
$ git show <tagname>
# -a指定标签名字，-m指定说明文字
$ git tag -a v0.1 -m "version 0.1 released" 1094adb

$ git tag -d v0.1
$ git push origin v1.0
$ git push origin --tags

# 删除远程标签
$ git tag -d v0.9
$ git push origin :refs/tags/v0.9
```

## git个性化定制

```bash
$ git config --global color.ui true
```

创建.gitignore

[gitignore配置](https://github.com/github/gitignore)

```bash
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
# 不排除.gitignore和App.class:
!.gitignore
!App.class
```


