---
title: git 常用命令笔记
date: 2025-12-29 16:48
tags:
- git
- 常用命令
categories:
- 软件开发工程师
---

# 更改上游url

你想更改 Git 仓库的 **上游（remote）GitHub 地址**，其实就是修改 `origin` 或其他 remote 指向新的仓库 URL。可以按下面步骤操作：

------

## 1. 查看当前 remote

```bash
git remote -v
```

输出示例：

```
origin  https://github.com/旧用户名/旧仓库.git (fetch)
origin  https://github.com/旧用户名/旧仓库.git (push)
```

------

## 2. 修改远程仓库地址

### 方法 A：直接修改已有 remote（推荐）

```bash
git remote set-url origin https://github.com/新用户名/新仓库.git
```

### 方法 B：先删除再添加

```bash
git remote remove origin
git remote add origin https://github.com/新用户名/新仓库.git
```

------

## 3. 验证修改

```bash
git remote -v
```

输出应显示新的 GitHub 地址：

```
origin  https://github.com/新用户名/新仓库.git (fetch)
origin  https://github.com/新用户名/新仓库.git (push)
```



## 4. 推送到新仓库

如果你想把本地分支推送到新仓库：

```bash
git push -u origin main
```

> 注意：如果你的分支不是 `main`，把 `main` 换成你自己的分支名。