---
layout: post
title: git正确使用姿势
date: 2023-08-11 08:16:00 +0800
category: Git
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/operation.jpg
icon: note
---


* content
{:toc}
# Git 的正确使用姿势与最佳实践：团队协作和版本控制的最佳实践 ｜ 青训营
> 这是我参与「第六届青训营 -后端场」笔记创作活动的的第5篇笔记
## 基本原理
1.每个仓库都有完整的操作记录，可以直接在本地提交代码

2.每次历史记录都是完整的文件快照，不只是记录改变量

## 提交过程
我们本地正在写的代码可以看作是一个工作区，然后提交的过程是先从工作区add到暂存区，再commit到本地仓库，最后再push到远程仓库

xx图片

### 工作区 
程序员进行开发(改动)的地方，是当前看到的。
说明：任何对象都是在工作区中诞生和被修改
### 暂存区
.git目录下的index文件, 暂存区会记录git add添加文件的相关信息(文件名、大小、timestamp…)，不保存文件实体, 通过id指向每个文件实体。

可以使用git status查看暂存区的状态。
### 本地仓库
保存了对象被提交过的各个版本

## 如何使用用golang和git进行团队开发
> 由于github的网速较慢，所以后面的远程仓库我们都是用gitee来演示，github的操作过程类似
### 1.每个成员应该下载好git版本控制器
直接百度git的安装方式，一般来说直接找到git，一直点击next，就可以了
### 2.