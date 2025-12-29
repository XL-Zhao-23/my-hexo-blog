---
title: git 版本控制系统笔记
date: 2025-11-18 17:51:00
tags:
- git
- 学习笔记
categories:
- 软件开发工程师
---



想要理解更深层的原理，可以看一看：

https://tonybai.com/2020/04/07/illustrated-tale-of-git-internal-key-concepts/

## 1. 概述

**版本控制系统的概念**

版本控制系统可以记录文件的变更历史以及多人协作

两个重点，一个是记录变更，一个是分支的概念，包括创建分支以及分支的合并

**版本控制系统的功能**

It allows multiple developers to work on the same codebase **simultaneously without overwriting each other's work**, providing a clear record of **who made changes and why**. Version control systems facilitate rollback to previous versions if issues arise, and they support **branching and merging**, which are crucial for **experimenting with new features and managing different stages of development.** 

**Git and other VCS**

- Mercurial: 架构类似Git、分布式、更加集中的方法、无hash、无追踪
- Subversion:  中心化的
- Perforce: 大型项目、商业化
- CVS: 过时的

## 2. 仓库和分支

### 2.1. 仓库

#### 2.1.1. 仓库初始化

`git init `创建一个git项目或者把一个文件夹变成git项目

`git config` 配置信息，有不同的级别

| **配置层级**         | **参数**       | **文件位置**                                 | **说明**             |
| -------------------- | -------------- | -------------------------------------------- | -------------------- |
| **系统级**           | `**--system**` | **Git 安装目录/etc/gitconfig**               | **对所有用户生效**   |
| **全局级（用户级）** | `**--global**` | **~/.gitconfig** **或** **$HOME/.gitconfig** | **对当前用户生效**   |
| **本地级（仓库级）** | `**--local**`  | **仓库文件夹/.git/config**                   | **仅对当前仓库生效** |

```
git config --global user.name "xxxx" 
git config --global user.email "xxx"
```

#### 2.1.2. 基本概念

**工作目录** 就是我们的项目文件夹中除了.git目录的内容

**暂存区** 存的是相对于“已提交”的代码所产生的变更，可以用来预览我们相较于上一次提交变更了哪些内容

**提交变更** 记录当前版本的**快照**

#### 2.1.3. 忽略

`.gitignore`在仓库目录下,这个文件中记录我们需要忽略哪些文件的变更

#### 2.1.4. 历史

`git log`查看提交的历史

`git log --oneline 2`简略的展示最近两次提交

`git log --graph`图形化的展示

### 2.2. 分支

分支本质是指向某个特定提交的**指针**

**查询分支**

`git branch --list`列出所有分支，并且会标出当前分支

**新增分支**

`git branch 新分支名称`创建新的分支

`git checkout -b 新的分支名称`创建并切换

`git switch -C 新分支的名称`创建并切换

**修改分支名称**

`git branch -m 分支名`修改当前正在使用的分支

`git branch -m 新名称 旧名称`修改当前某一个分支的名称

注意：

修改分支名称和删除分支，都要同步修改远程分支，修改就是先删除再推送新分支 

git push origin --delete old-name

git push origin new-name

设置追踪

git push --set-upsteam origin 新名称 

**删除分支**

`git branch -d name`删除已经合并的本地分支

`git branch -D name`删除没有合并的本地分支

**切换分支**

`git checkout 分支名称`切换到另一个分支，此时工作目录的内容会从当前分支切换到目标分支

注意：当我们切换的时候，暂存区或者工作目录的修改如果和目标分支有冲突，会报错，需git stash 进行暂存

**分支合并**

先切换到main分支，在main分支上执行git merge <source-branch>，解决冲突，然后commit。最后删除原来的分支。例子：

将自己的分支合并到主分支

- git merge main

解决冲突

git commit 会自动生成合并信息





更新main，再继续开发自己的分支

- 先提交本地的代码

git add .

git commit -m ""

- 切换到主分支，更新最新的代码

git checkout main

git pull

- 切换回自己的分支，然后把main合并到自己的分支

git merge main

解决冲突

git commit 会自动生成合并信息

## 3. 基础功能

### 3.1. Github基础

**创建github账号**

略

注意：双因素验证的原理

通过两种方式确认身份，常见的实现方式有账密验证+验证码验证，验证码常见的是手机短信验证码，github用的是一种基于  Authenticator App  动态生成验证码的方式

验证码生成的公式：TOTP = Truncate(HMAC-SHA1(Secret, CurrentTimeStep))

github 生成一个 secret， 用二维码展示，然后用 Authenticator App  扫描， Authenticator App  和Github 共享一个secret秘钥，每30秒依据依据时间戳生成一个新的验证码

**Profile**

创建一个和账号同名的仓库，在readme中写自己的介绍，github会自动将这个readme的内容展示在主页上

**创建仓库**

略

### 3.2. Git Remotes

#### 3.2.1. 克隆仓库

**HTTPS**

`git clone https://github.com/账户名/仓库名.git`这是https的连接方式，需要账密登录

私有仓库，需要账密验证，或者使用token

- GitHub → Settings → Developer settings → Personal Access Tokens → Tokens (classic) → Generate new token 有效期设置
- 勾选需要的权限（例如 repo 权限）
- 生成后复制 Token
- 保存git

```git
# Linux / macOS
git config --global credential.helper cache
# Windows
git config --global credential.helper manager
```

**SSH**

`git clone git@github.com:账户名/仓库名.git`通过SSH登录，需要配置这些内容：

本地生成密钥对：

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

-t ed25519 是加密方式

-C 是注释

Github配置公钥：

- 打开 GitHub → **Settings → SSH and GPG keys → New SSH key**
- 打开 `id_ed25519.pub`，复制内容粘贴进去
- 命名（Title） → 保存

**引申**

- **PAT**：像“可控密码”，通过 HTTPS 验证身份，泄露后可撤销。https通过TLS保障信道安全，通过PAT验证身份
- **SSH**：基于密钥对的加密签名验证，通过 SSH 建立安全通道，私钥不在网络中传输，更安全。SSH既能表明身份又能保障信道安全。
- 另外，实际体验是SSH不会网络拥堵导致443

#### 3.2.2. 管理远程仓库

`git remote -v`查看所有的远程仓库

```
git push
git pull
git fetch
```

例子：

```git
# 1. 获取远程更新
git fetch origin

# 2. 查看远程分支差异
git log main..origin/main --oneline
git diff main origin/main

# 3. 合并远程更新
git merge origin/main   # 或 git rebase origin/main
```

git pull = git fetch + git merge

git pull 报的合并冲突，其实就是merge的

### 3.3. 合并策略

#### 3.3.1. Fast-Forward vs Non-FF

**Fast-Forward Merge**

情况1 ：master 有3个提交C0，C1，C2，此时新建一个分支iss53，ss53指向C2，然后iss53更新，提交C3，iss53指向C3

![img](图片.png)

情况1下merge，就只需要将master移动到c3



**Non-FF Merge 或 3 Way merge**

情况2：在情况1的基础上，maste分支有新的提交C4

![img](图片.png)

这种情况下合并，就需要创建一个新的提交C6，用来记录如何合并C2版本和C3版本，master移动到C6

![img](图片.png)

#### 3.3.2. Rebase

参考：

[https://git-scm.com/book/zh/v2/Git-%e5%88%86%e6%94%af-%e5%8f%98%e5%9f%ba#_pre_merge_rebase_work](https://git-scm.com/book/zh/v2/Git-分支-变基#_pre_merge_rebase_work)

##### 3.3.2.1. 情况1：`git rebase 新基底`

![img](图片.png)

```git
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

![img](图片.png)

```plain
$ git checkout master
$ git merge experiment
```

![img](图片-1766997110841.png)

##### 3.3.2.2. 情况2：`git rebase --onto 新基底 老基底 分支` = “把分支上不在老基底的提交摘下来，放到新基底上”  

![img](图片-1766997110907.png)

git rebase --onto client server master

![img](图片-1766997110985.png)

git checkout master

git merge client

![img](图片-1766997111052.png)

##### 3.3.2.3. 情况3：git rebase 新基底 分支

git rebase master server

![img](图片-1766997111131.png)

git checkout master

git merge server

git branch -d client

git branch -d server

![img](图片-1766997111207.png)

##### 3.3.2.4. rebase 的风险

 不要对已经存在于仓库之外、且其他人可能基于其进行开发的提交执行 rebase。  

![img](图片-1766997111278.png)

如何解决：用rebase解决rebase



##### 3.3.2.5. Rebase VS Merge

既然你已经看到 rebase 和 merge 的实际操作，你可能会想哪一个更好。在回答这个问题之前，我们先退一步，讨论一下“历史”意味着什么。

一种观点认为，你的仓库提交历史是 **实际发生过的记录**。它就像一份历史文档，本身很有价值，不应该被篡改。从这个角度看，修改提交历史几乎是一种亵渎；你是在“篡改”实际发生的事情。那么，如果有一连串混乱的 merge 提交呢？那就是事情发生的方式，仓库应该保存这些历史，以供后人参考。

另一种观点则认为，提交历史是 **项目是如何被创建的故事**。你不会出版一本书的初稿，那么为什么要展示混乱的工作过程呢？在开发项目时，你可能需要记录所有的错误尝试和死胡同路径，但当你要向世界展示你的工作成果时，你可能希望讲述一个更清晰的故事，展示从 A 到 B 的过程。在这种观点下，人们会使用 rebase 或 filter-branch 等工具，在提交合并到主分支之前重写提交，以便以对未来读者最清晰的方式讲述项目的发展过程。

那么，merge 和 rebase 哪个更好呢？希望你能明白，这个问题没有简单答案。Git 是一个功能强大的工具，允许你对提交历史进行多种操作，但每个团队和每个项目的情况都不同。既然你已经了解了这两种操作的工作原理，就由你决定哪种方式最适合你的具体情况。

你也可以两者兼得：在 push 之前对本地更改进行 rebase 来清理你的工作，但**绝不要对已经推送到远程的内容进行 rebase**。

#### 3.3.3. Squash 压缩提交

把多个提交整合成一个

##### 3.3.3.1. 交互式rebase

```
**git rebase -i **
```

例子：

git log --oneline

git rebase -i HEAD~3

会弹窗提示

##### 3.3.3.2. merge --squash 压缩一个分支并merge

```
git merge --squash 
```

git checkout main

git merge --squash feature-branch

git commit

#### 3.3.4. Handling Conflict

在IDE中很方便

#### 3.3.5. Cherry Picking Commits

把另一个分支的某一个提交接到本分支

```git
upstream/release/next: ---X---N(0c8c3320)
upstream/release/3.10: ---X
upstream/release/3.11: ---X
upstream/release/3.12: ---X
upstream/release/3.13: ---X
upstream/release/3.14: ---X

git checkout -b add-docs-zh-3.10 upstream/release/3.10
git cherry-pick 0c8c3320
git add .
git commit -m "docs(zh): update config.json for new tutorials"

对每个分支都进行这种操作
upstream/release/next: ---X---N(0c8c3320)
upstream/release/3.10: ---X---N(0c8c3320)---C1
upstream/release/3.11: ---X---N(0c8c3320)---C2
upstream/release/3.12: ---X---N(0c8c3320)---C3
upstream/release/3.13: ---X---N(0c8c3320)---C4
upstream/release/3.14: ---X---N(0c8c3320)---C5
```

先压缩为一个commit，然后再cherry到其他分支

### 3.4. 基于Github的协作

## 4. 最佳实践

### 4.1. 文档

### 4.2. 团队协作

### 4.3. github projects

## 5. 中级功能

### 5.1. 历史

### 5.2. 回滚

### 5.3. 差异

### 5.4. rewriting history

### 5.5. tagging

### 5.6. hooks

### 5.7. git patch

### 5.8. 子模块

## 6. Github工作流

### 6.1. Gtihub CLI

### 6.2. Github Actions

## 7. 高级功能

## 8. Github 开发者工具

### 8.1. Github API

### 8.2. Creating APPs

### 8.3. webhooks

## 9. 更多

### 9.1. Github Copilot

### 9.2. Github Gists

### 9.3. Github Models

### 9.4. Github Packages

### 9.5. Github Marketplace

### 9.6. Github Codespaces

### 9.7. Github Education

### 9.8. Github Security

### 9.9. Github Sponsors