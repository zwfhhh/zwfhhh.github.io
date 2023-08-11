---
layout: post
title: CRM项目笔记
date: 2023-08-11 8:08:00 +0800
category: Project
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/project.jpg
icon: code
---


* content
{:toc}
# CRM项目笔记

## classpath的作用

classpath指的是target下的classes，在classpath前缀后的资源都都要到classes文件中寻找。而在idea项目中只有被标记为Resource Folders的文件夹才能被添加到classes下

改html名字为jsp之前一定要设置下编码，否则当页面中中文会出现乱码

## 什么时候需要创建新的controller

当返回的页面是同一个或者同一个目录下，就写在同一个controller下，否则，写在新的controller类下

requestmapping中的路径要和返回的路径目录一样

替换一些相同的部分CTRL+R快捷键

## 当相应回来页面时候，需要刷新整个页面，则使用同步请求，如何只需要刷新局部页面，则使用异步请求

1。返回页面之后需要全局刷新 使用同步

2.返回页面之后只需要局部刷新，使用异步

3.返回页面之后可能局部，也可能全局，使用异步请求 例如：登录

## service层中的类根据这个表来创建，一个表对应一个service

Controller层中根据这个返回页面的目录是否相同来判断是否创建新的Controller类

> 用这个思想来在mvc项目中使用baseservlet时候也能解决这个有时候 的跳转报错问题

## 为啥要将controller的相应信息变为json格式

因为ajax只能处理json格式的相应信息

当请求为同步时候，返回html格式信息没问题，但是如果是异步请求，需要是json格式的返回信息

jar包不可以独立运行，plugin插件可以独立运行，点击改jar包，可以独立运行

通过新建一个modul的方式，来给项目安装这个mybatis的逆向工程插件（通过这个表来生成javabean的类，mapper接口，mapper.xml）

当controller需要返回这个json格式的字符串时候，可以将返回类型写成Object

## jquery中获取指定元素的指定属性的值

选择器.attr("属性名")用来获取哪些值不是true or false的属性的值

选择器.prop("属性名")用来获取值是true or false;的属性值

## 使用jstl可以直接取出session中的值

${sessionscop.'k'.‘属性名’}

![image-20230430211553087](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230430211553087.png)

## 实现10天记住密码

### 拦截器和过滤器的联系

![image-20230501155223177](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230501155223177.png)

拦截器是框架对过滤器做了封装的，拦截器一般用于一些比较复杂的拦截工作，过滤器一般用在一些比较简单的过滤工作，一般ssm框架里都是使用拦截器

## 重定向和转发

如果是借助框架帮助你请求和转发，那么他会帮助你自动加上项目名字，反之，如果通过自己来重定向，那么需要自己去加上/项目名字

## 模态窗口

> 本质:和原来的页面是一个页面，只是显示的位置不同

控制模态窗口的显示和隐藏

方式一: 通过data-toggle="modal" data-target="模态窗口的id"

方式二：通过js函数控制；选择器（选中的div）.modal("show")//显示选中的模态窗口

​				通过js函数控制；选择器（选中的div）.modal("hide")//关闭选中的模态窗口

好处：可以做初始化工作

方式三：通过标签的属性data-dismiss=""  点击之后模态窗口自动关闭

## 正则表达式

```
正则表达式
1.在js中定义一个正则表达式
2.匹配某一位 【abc】
3.$匹配结尾
4^匹配开头
5.{}匹配次数
6.{m,n}匹配m到n次
7.{m,}匹配m或更多次
*匹配0或者多次  {0，}
+   1   多     {1，}
？   0   1     {0，1}
```

### jquery对象可以通过.get(0)的方式转为dom对象

![image-20230501214615748](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230501214615748.png)

## 前端插件使用步骤

1.引入开发包 js,css

​		下载开发包，拷贝到webapp目录下

​		把开发包引入jsp文件中，<link>,<script>

2.创建容器

3.容器加载完成==之后==，对容器调用函数

> 一个class可以对应多个名字，中间用空格分离

```
$(".mydate").datetimepicker({
   language: 'zh-CN',
   format: 'yyyy-mm-dd',
   minView: 'month',
   initialDate: new Date(),
   autoclose: true,
   todayBtn: true,
   clearBtn: true
})
```

## 分页查询

必备三种需求

1.开始必须显示第一页的分页页面

2.可以查询

3，可以切换页号

## 外连接和内连接的选择

如果外键可能为空，则使用外连接，反之则使用内连接

![image-20230502104228643](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230502104228643.png)

## 当给button type='submit'加上点击事件，点击之后可能没有反应的问题

需要将type改为button

## 选择器。xxx(function{})这种方式，==只能给固有元素加事件==

![image-20230502220348547](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230502220348547.png)

上面这种非固有的元素添加事件是无效的

解决方法：使用jquery的on函数，父选择器。on(”事件类型"，子选择器，function{

//js

})

父元素：必须是固有的元素，(范围越小越好) 可以为直接父元素，也可以为间接父元素

## update delete insert 的mapper。xml中默认没有resultType这个属性

## 最后写前端一般就两件事

1.有参数封装参数，有格式验证写格式验证，最后发请求

2.接收返回的相应（页面或者json）

只有遍历作用域中的数据采用jstl,否则使用js or jquery

![image-20230503092240232](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230503092240232.png)

## value和val()

value是属性 val()是函数

## 报错Mapper method 'com.bjpowernode.crm.workbench.mapper.ActivityMapper.deleteActivityByIds attempted to return null from a method with a primitive return type (int).

![image-20230503101652276](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230503101652276.png)

## 封装参数

1.如果做查询 ，或者参数之间不属于一个对象，则封装为一个map

2.如果写数据，并且参数本来就是属于同一个对象，则封装为一个对象

## 点击发送下载的请求只能使用同步的请求

应为如果是异步请求，ajax无法解析返回的文件信息

发同步请求的三种方式

1.地址栏

2.form表单

3.超级链接

## 如何解决从内存到磁盘或从磁盘到内存的低效率问题（write方法）

直接不经过磁盘，直接从内存到内存

## 文件上传表单条件

1.表单组件标签只能用：<input type='xxxx'>

2.请求方式不能用get

> get请求对参数的长度有限制，效率高
>
> post请求可以提交二进制数据，对参数长度没有限制，安全

3.表单的编码格式必须为: multipart/form-data

对于ssm项目需要配置springmvc的文件上传解析器

row.getLastCellNum()是最后一行的下标加1

sheet.getLastRowNum()是最后以列的下标

文件下载的方式只能是同步的，上传可以是异步的，也能是同步的

## 还没解决的问题

![image-20230506212646429](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230506212646429.png)

## 给标签加属性

如果是表单组件 优先使用value属性

如果不是，则优先使用自定义标签

## 不同对象获取value值

dom对象.value

jquery对象.val()

如果是自定属性，获取时候只能用jquery对象  ：jquery.attr("属性名")

## 标签定位两个目的

1.使用标签保存数据

如果是表单组件 优先使用value属性

如果不是，则优先使用自定义标签

2.定位标签

id>name>自定义标签
