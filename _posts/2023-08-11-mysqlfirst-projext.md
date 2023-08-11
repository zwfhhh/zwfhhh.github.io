---
layout: post
title: MysqlFirst项目过程中遇到的困难
date: 2023-08-11 08:32:00 +0800
category: Project
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/project.jpg
icon: note
---


* content
{:toc}
# MysqlFirst项目过程中遇到的困难

## 1，我maven项目中，实体类属性的数据类型为基本数据类型，在操作select语句时报出IllegalArgumentException的错误，原来对应的数据库中该属性值不能为null否则在做相关查询时 会报该异常

解决办法：实体类字段属性不要使用基本数据类型（比如int ，long等），要使用Long，Integer等包装类
修改后就没报此错误了。

## 2.div中没有value属性，所以如果在div中使用value，那么这个值获取不到，为""或者undefine

解决方法：使用input中的type为hidden

## 3.c:foreach循环出来的只能获取到第一个的里面的值，如何获取c:foreach的值？

==写到点击菜系跳转servlet这里==

如何做批处理

![image-20230411171942259](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230411171942259.png)

## 如何来存储这每个菜品，用一个map,key为foodid ，v为数量

session中的k为tableid，值是shopcart 里面的k是foodid,v是foodnum

用session来存储购物车

使用jqury时候通过$().val()的方式获取值

使用document通过.value;获取值

servlet中通过getServletContext().getContextPath()获取前面的路径

当使用一对多关系的时候需要使用多表联查

这也是为啥需要设计两个order和orderdetail的原因，通过这个对应的这个orderid可以查到在这个里面有哪些食物

==做到显示购物车里面的物品这里，需要写一个servlet来通过tableid来查找购物车中的所有食物==

## 在button中添加onclick不起作用

解决方法:type需要该为submit通过form表单提交

## 在post方法中不可以直接转发到servlet，会发生405

解决方法：使用重定向

## 在post中中文传递出现乱码

解决方法：使用一个过滤器处理字符问题

记得form表单是获取的name属性,否则会获取失败

## Java Web重定向参数中文乱码问题

解决方法：在[重定向](https://so.csdn.net/so/search?q=重定向&spm=1001.2101.3001.7020)代码之前，将参数值用以下代码编码一下即可。

message = URLEncoder.*encode*(message, "UTF-8");

```java
@WebServlet(urlPatterns = "/check")
public class CheckServler extends HttpServlet{
 
	@Override
	public void doGet(HttpServletRequest request, HttpServletResponse response) 
			throws ServletException, IOException {
 
		String username = request.getParameter("username");		
		String message = null;
		
		if(username == null) {
			message = "Please input username.";
		}else {
			message = "Hello, " + username; 
		}
 
		message = URLEncoder.encode(message, "UTF-8");
		response.sendRedirect("/helloapp/output?msg="+message);		
 
	}
}
 
 
@WebServlet(urlPatterns = "/output")
public class OutputServlet extends HttpServlet {
 
	@Override
	public void doGet(HttpServletRequest request, HttpServletResponse response) 
			throws ServletException, IOException {
 
		response.setContentType("text/plain; charset=UTF-8");
		
		String msgString = request.getParameter("msg");
		
		PrintWriter outPrintWriter = response.getWriter();
		outPrintWriter.println(msgString);
		outPrintWriter.close();		
	}
 
}
```

## 在写项目的过程中突然jsp页面中的css不起作用，重启电脑也没用

原因：

![image-20230415232323862](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230415232323862.png)

原先是写的html页面，开头有一个html页面的声明，去掉就可以

## 购物车中需要完善的部分

1.在点击这个菜系的分类之后的菜品页面的下单键消失,几号餐桌也消失了

2.当前的购物车在转发之后的页面需要完善，转发之后的下单键位失效

3.在付款之后的订单只是在session中清空了，还没有在数据库中清空

4.购物车中的取消键还没有写

5，后面可以考虑这个菜品的加入功能

问题最严重的值页面太少导致传入的参数堆积再一起，进一步导致传递参数紊乱

逻辑混乱，这也是导致上面三个问题的根本原因

> 注意理解购物车的实现逻辑、

## 进度记录

现在需要解决的问题
1.不可以连续两次或多次下单同一个商品，否则会报错  ==已解决==

原因：get（）中传入的参数和Map的k的类型对不上

解决方法：将传入的参数强转类型

2.订单详情中的取消相应时间还没做





3.订单详情还不是显示的对应的餐桌的订单详情   ==解决==

![image-20230416164901057](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230416164901057.png)

原因：sql语句中值忘记传入，所以每次传入一样的

解决:修改sql语句，set值

![image-20230416164929104](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230416164929104.png)

这句之前没写

4.付款按钮报错

5.mysql+c3p0卡顿得现象

# 前端，后端，前台，后台的区别及联系

## 前端:

前端代码就是项目在**客户端运行时跑的代码**，前端技术包括，html,css,[javascript](https://so.csdn.net/so/search?q=javascript&spm=1001.2101.3001.7020).技术以及各种框架，类库等。

- 前端主要针对客户端（pc,移动），前端工程师的责任就是用前端技术把美工（ui）设计好的图和模型用HTML转化成页面，同时**处理用户在页面上的操作，将数据发送给后端或者请求后端api返回数据，然后将数据展示到页面。**
- 前端这个词，更加**偏向于技术**。在某种意义上你也可以将前端理解为是客户端，也就是电脑或者手机。站在用户的角度可以将前端理解为用户看到的网页以及应用页面。前端可以说是视图层，但也不能说完全是view layer。
- 因为现在的前端技术发展很快，前端工程师也承担了越来越多的事情。前端也有简单的逻辑层，比如，dom操作。前端开发者要更注重用户的体验（页面美观，加载速度等）。

## 后端:

后端代码是**运行在服务器上的**。后端技术有 php,java,.net等语言。同时，各种语言都有自己的技术框架。后端主要负责整个项目的业务逻辑处理。

- 例如，前端页面通过api 向后端发送一个请求，要求返回一年级一班所有同学的个人信息。后端收到这个请求后需要到数据库取出一年级一班所有同学的个人信息（可能是一个数组），然后response给前端，前端收到这个数组通过数据转化将所有同学的个人信心展示到页面。
- 后端有很多种技术同时存在，一般根据不同的项目选择不同的技术栈，记住，后端代码是运行在服务器上的也就是你的网站网址所指向的ip地址。这也是前后端最重要的区别之一。**后端偏向于逻辑，数据处理，权限等**。

## 前台:

说到前台，很多人会把前台和前端混为一谈。**前台是直接指向用户的**。

- 比如，我们看见的网页。大家用的app等所有的互联网应用展示给用户的都叫做前台。
- **前台它不是技术**。他和技术不搭边。很多人都会理解前台是和技术有关系。

## 后台:

后台区别于前台就是，虽然后台也是面向用户，但是，记住。**后台面向的是管理员**（也就是网站，app的数据维护人员）。后台通过维护管理数据来保证网站和应用的正常使用。

- 例如我们常常听到的某某管理系统。就是属于后台。
- 其实，**前后台的实现都要利用前后端技术来实现。**

## 使用mysql+c3p0出现非常卡顿的现象，先使用原来的

解决方法：将自动注入值和反射类中使用c3p0得位置修改了，因为这个类被调用得频率太高了，dao层任然可以使用c3p0,建议后面需要统一使用一种，便于后面项目得移植，==后面来解决这个mysql+c3p0卡顿得现象==





## 目前问题：

1.取消按钮需要完善

取消按钮中得传值没问题，但是==显示存在问题==

2.还有就是购物车==应该只有那几种菜品，不能重复==，数量累加，==现在==是每次都是一个，没有累计，==不断重复==

数据库得数据存在一定得问题

经过检查，每次得shopcar中得值没问题，==但总是有几个每点过得菜和订单再点击下单后直接生成==

3.付款按钮点击之后，该餐桌得所有菜品都被清空，下次点菜，购物车重新开始

## 接下来:

取消按钮的功能 ，点一个有时候会重复上次点了的

## 部署到服务器上80端口上的tomcat无法使用的问题

[(724条消息) 解决Tomcat服务器端口被占用问题_tomcat端口被占用怎么解决_Enter-IT的博客-CSDN博客](https://blog.csdn.net/qq_41840027/article/details/86833255)

==记得关掉iis服务==

现在将全部使用c3p0，解决运行缓慢的问题

## 如何在服务器中看日志

靠报错的话，logs下面看后缀为err的日志

## NT kernel & System 占用占用80端口

https://blog.csdn.net/kaishizige/article/details/86485946

## 服务器上连接数据库失败要注意驱动包和本地的数据库是否匹配
