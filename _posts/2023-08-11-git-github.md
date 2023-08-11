---
layout: post
title: git和github的交互问题
date: 2023-08-11 08:11:00 +0800
category: Git
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/operation.jpg
icon: note
---


* content
{:toc}
**前言：在完成git的配置<br>解决完github的进入速度慢的问题<br>在GitHub上完成了远程仓库的创建**
***
#首先需要将远程仓库克隆到本地仓库
##现在，远程库已经准备好了，下一步是使用命令git clone克隆一个本地库了。如下所示：  
![](http://img1.sycdn.imooc.com/59c1d9860001e0d806370127.png)  
## <font color='#FF0000'>注意：后面的地址是你自己仓库的地址</font>##
# git如何于github进行交互
## 1.找到你克隆过来的仓库，创建一个文本test.txt,并写入一段话（随便写！），空的文本传输可能会出现异常  
## 2.将文件添加到暂存区  
    git add test.txt
## 3.将暂存区添加到仓库区（说明信息会在远程仓库上有注解）
    git commit -m "说明信息"
## 4.将本地提交到远程仓库  
     第一步：git pull
     第二步：git push origin master
>git pull 是为了从远程库获取到本地，相当于做了一个同步更新  
>git push origin master 直接将你本地克隆仓库的文件提交给远程仓库
