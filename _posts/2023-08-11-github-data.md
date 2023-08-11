---
layout: post
title: github加载慢的问题
date: 2023-08-11 08:18:00 +0800
category: GitHub
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/operation.jpg
icon: note
---


* content
{:toc}
# github加载过慢的原因
## 1，CDN，Content Distribute Network，可以直译成内容分发网络，CDN解决的是如何将数据快速可靠从源站传递到用户的问题。用户获取数据时，不需要直接从源站获取，通过CDN对于数据的分发，用户可以从一个较优的服务器获取数据，从而达到快速访问，并减少源站负载压力的目的。



## 2，为什么访问速度慢、下载慢？答：github的CDN被某墙屏了，由于网络代理商的原因，所以访问下载很慢。ping github.com 时，速度只有300多ms  
***
# 解决方法
# 1.以管理员方式运行cmd ，然后进入：C:\Windows\System3\driver\etc目录下。
# 2.然后输入：notepad hosts 。
# 3.输入完成之后会弹出编辑hosts文件的窗口，编辑完成直接保存即可！

![](https://img-blog.csdnimg.cn/bb5e3b0467104a58972e2fc5dd0035da.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Y2D54Gv5LiN5piv54Gv,size_20,color_FFFFFF,t_70,g_se,x_16)  
***
# 对于如何取得这个编辑进入hosts的内容的步骤
## 1.点击[链接](http://github.global.ssl.fastly.net.ipaddress.com/#ipinfo),记录下IP地址  
![](https://img-blog.csdnimg.cn/img_convert/1e8f0e4efd3fb9c8ba344d81a4f2e220.png)   
## 2.点击[链接](http://github.com.ipaddress.com/#ipinfo),再次记录下IP地址  
![](https://img-blog.csdnimg.cn/img_convert/1e31bc63993195a9e1267dd0481f66a8.png)  
## 3.第三步就是最开始介绍的对hosts的操作，在hosts文件末尾加入   
 `199.232.69.194	http://github.global.ssl.fastly.net`
`140.82.112.4  http://github.com`
