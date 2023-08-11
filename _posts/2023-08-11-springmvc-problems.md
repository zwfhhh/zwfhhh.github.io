---
layout: post
title: SpringMvc遇到的问题
date: 2023-08-11 08:35:00 +0800
category: SpringMvc
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/springmvc.jpg
icon: note
---


* content
{:toc}
# 静态文件无法被处理问题
## 需要同时加上<mvc:default-servlet-handler>和<mvc:annotation-driven>
## 这样可以保证在处理动态资源时用dispacherservlet,在处理静态资源时用tomcat默认的defaultservlet.
***
## 在控制器中用Model设置资源到request域中，就是为了在html中可以调用域中的属性
***
## 处理浏览器不能发送put和delete请求的方法 
### 1.在html中的加入表单中(记得不是加在控制器方法中)的name属性为_method,
### 2.value为你put或者delete.
### 3.method属性必须为post.
## 记得上面一个方法使用的前提是加上一个hiddenhttpmehtodfilter过滤器
[具体配置方式在这](https://www.cnblogs.com/codingcc1/p/11073297.html)
***
# web.xml和springmvc.xml的区别
## 1.web.xml是整体的配置，是系统级别的。在里面可以配置欢迎和错误页面，为servlet命名和定制url，定制初始化参数等高级配置。
## 2.springmvc.xml是使用springmvc的一些配置，包括解析，扫描和拦截view等。
[具体web.xml pom.xml springmvc.xml的区别](https://blog.csdn.net/weixin_44296929/article/details/102553478)
#web.xml文件中一般配置三个东西
## 1.编码过滤器
## 2.HiddenHttpMthodeFilter过滤器
## 3.dipacherservlet控制器
# ajax问题
## 通过vue的页面交互写法，在点击button后，调用axios请求数据库，发送ajax请求，再通过dispacherservlet控制器处理ajax请求，再控制器中调用response的gerwriter().write()来局部刷新。
# 使用@ResponseBody注解响应浏览器json格式数据
## 1.导入jackson的依赖
## 2.再springmvc配置文件中设置<mvc：annotation-driven/>
## 3.将返回的java对象直接作为控制器方法的返回值，可以直接转换为json字符串，响应到浏览器。
## 注意：实体类和map类型的都是直接转换为json字符串，list转换为json数组。

## ModelAndView
### model指的是要跳转的页面
### view指的是要返回的数据

