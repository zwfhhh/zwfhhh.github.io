---
layout: post
title: 用layui写用户登录页面遇到的问题
date: 2023-08-11 09:13:00 +0800
category: Servlet
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/servlet.jpg
icon: code
---


* content
{:toc}
# 用layui写用户登录页面遇到的问题



## 1.在layui-row下面的layui-col-md还是换行

原因：link标签和script标签中的type属性没写，导致应该是script或者这个css没有识别出来

解决办法：link标签里面加上type为text/css,  script标签中加上type为

## 2.如何让页面随着页面的缩小放大而缩小放大



## 3.我的布局![image-20230225105121796](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230225105121796.png)

## 4.如何变成圆角

```
-webkit-border-bottom-right-radius: 20px
```

## 5.如何怎么让网页不随着浏览器窗口大小改变内容不发生改变

在你需要不改变的盒子外面加一层盒子

## 6.如何让一个html页面兼容各种浏览器

基本上都要加一个margin:0 padding = 0;

[HTML`CSS_网站页面不同浏览器兼容性问题解决 - 菲比月 - 博客园 (cnblogs.com)](https://www.cnblogs.com/phoebeyue/p/9226537.html)

## 7.如果用contaier会导致这个全部放在中间

## 8.如何改变表单位置（上下左右居中）

![image-20230301190317066](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230301190317066.png)

## 9.写那个layui-row一定要加上height，不然显示不出颜色和位置

![image-20230301190449186](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230301190449186.png)

