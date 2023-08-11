---
layout: post
title: html基础知识
date: 2023-08-11 08:25:00 +0800
category: HTML
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/basic.jpg
icon: note
---


* content
{:toc}
# html

## 标签常用属性

注意属性之间用空格分离，值用字符串

## id标签

每一个元素可以设置一个id属性值，这个是唯一的，不可有重复

## name

这个可以有重复，js可以通过name属性值查找元素，如果有多个则为数组

## class

这个也可以重复，样式名称可以有多个，之间用空格分离

## style

> 定义规则：样式名1：值；样式名2：值；样式名3：值；

只是当前元素的样式，每组之间使用分号分离，样式值通常不使用双引号

## value

一般用在文本框，文本域，下拉框，option,单选框里面，用来传值给后台servlet

## action

指定哪个url接受数据，指定了表单每一个控件的value交给哪个程序

## 样式

如果一个页面中需要使用一些相同的样式，可以把这些样式定义在一个css文件中

## iframe

> 说明：使用了iframe,多个页面组合而成，以后使用js要注意父子的关系（当前页面父页面，iframe页面子页面）

用于嵌入其他页面，请求页面时候，浏览器向服务发起多次请求（并行执行的方式），最后效果为

在当前页面上和iframe里面的页面组成一个新的页面

### 如果需要在点击一个链接之后加入iframe页面，效果类似于ajax,需要如何做

在a标签上加上属性target指向这个iframe的name属性值，覆盖原来iframe的页面，刚开是显示的frame中的页面，后来点击链接之后是显示的链接之后的src中的

# MVC and servlet

**Model**

（模型）：**数据模型，提供要展示的数据，因此包含数据和行为，主要提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。主要使用的技术：数据模型：实体类（JavaBean），数据访问：JDBC，Hibernate等。

**View**：

负责进行模型的展示，一般就是我们见到的用户界面，比如JSP，Html等

**Controller**

接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。主要使用的技术：servlet，Struts中的Action类等。

  MVC是一个框架模式，它强制性的使应用程序的输入、处理和输出分开。使用MVC应用程序被分成三个核心部件：模型、视图、控制器。它们各自处理自己的任务。最典型的MVC就是JSP + servlet + javabean的模式。

![image-20230307100659296](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307100659296.png)

——————————————————————————————————————————--

以上为预备

==以下为课上==

model:javabean

view:jsp页面

controller:

> servlet         其中使用创建的类定义对象，然后转发调用页面（视图）

优势：程序逻辑和用户界面分工

## ==项目要求==

访问项目的时候不能直接通过网页请求，都通过servlet转发

jsp页面代码中不使用java语句（<%%>），通过在jsp中使用el表达式或者jstl实现html来实现相关的功能

## url请求

servlet的标志：继承httpservlet

servlet使用url映射,使用浏览器经行访问

### 映射url常用的两种方式

1.使用在类头加@webservlet("")注解的方式

![image-20230307103750787](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307103750787.png)

字符串中的第一个/的含义：项目的根路径

2.在web.xml文件中配置

![image-20230307104209697](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307104209697.png)

url-pattern:设置的访问路径，和注解类似

注解：这两种方式不冲突，一个servlet可以有多个映射路径

![image-20230307105103133](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307105103133.png)



# el表达式

**EL访问数据**：

​    ·格式：如${user.userName}、{user[userName]}、${user[“userName”]}，这种方式可以用于读取数据、List、Map或者是对象容器中的数据。

   ·也可以运用[]或者., EL表达式中的点运算符，用于访问JSP页面中某些对象的属性，如JavaBean对象、List集合、Array数组等，EL表达式中的方括号运算符与点运算符的功能相同，都用于访问JSP页面中某些对象的属性。但是当获取的属性名中包含一些特殊符号，如“_”或“-”“?”等并非数字或字母的符号，就只能使用方括号运算符来访问该属性。

==**EL存取范围**==：

 ·默认从page中找

 ·接着依次是request、session、application

 ·找到返回值，找不到则返回null
