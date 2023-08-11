---
layout: post
title: servlet基础知识
date: 2023-08-11 08:45:00 +0800
category: Servlet
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/servlet.jpg
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

![image-20230315091516459](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230315091516459.png)

# MVC and servlet

**Model**

（模型）：**数据模型，提供要展示的数据，因此包含数据和行为，主要提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。主要使用的技术：数据模型：实体类（JavaBean），数据访问：JDBC，Hibernate等。

**View**：

负责进行模型的展示，一般就是我们见到的用户界面，比如JSP，Html等

**Controller**

接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。主要使用的技术：servlet，Struts中的Action类等。

  MVC是一个框架模式，它强制性的使应用程序的输入、处理和输出分开。使用MVC应用程序被分成三个核心部件：模型、视图、控制器。它们各自处理自己的任务。最典型的MVC就是JSP + servlet + javabean的模式。

![image-20230307100659296](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307100659296.png)

*****



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



页面发起请求之后

![image-20230307105103133](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230307105103133.png)

注解：这两种方式不冲突，一个servlet可以有多个映射路径，但是==不可以出现同名的url==

可以通过修改虚拟路径的后缀来迷惑访问人员

## 数据的输入输出

数据输入之后servlet接受，完成业务逻辑

输入输出的方式： get or post

### get方式输入值

1.浏览器发送请求

2.form表单

### post方式

1.form表单

### 接受数据

get在servlet中的doGet中接受

post在servlet中的doPost中接受

### get方式请求下servlet控制器得到值

get请求中在浏览器中发起请求可以通过？加属性名的方式来向doGet方法传递值

多个值通过在多个参数之间添加&连接

### post传值 接受表单中的值

通过属性中的name属性传值

getparameter方法返回的都是string类型

# el表达式

**EL访问数据**：

​    ·格式：如${user.userName}、{user[userName]}、${user[“userName”]}，这种方式可以用于读取数据、List、Map或者是对象容器中的数据。

   ·也可以运用[]或者., EL表达式中的点运算符，用于访问JSP页面中某些对象的属性，如JavaBean对象、List集合、Array数组等，EL表达式中的方括号运算符与点运算符的功能相同，都用于访问JSP页面中某些对象的属性。但是当获取的属性名中包含一些特殊符号，如“_”或“-”“?”等并非数字或字母的符号，就只能使用方括号运算符来访问该属性。

==**EL存取范围**==：

 ·默认从page中找

 ·接着依次是request、session、application

 ·找到返回值，找不到则返回null
****

以上为预备

以下为课上

****

## 页面启用el表达式

加上iselignored="false"

## 使用方式

> 表达式格式${变量}

1.可以放在网页中直接显示内容

2.可以放在标签中显示内容或者==做属性的值==

3.通过在el表达式中写==三目运算符==来做判断

![image-20230308223948293](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230308223948293.png)

注意：el表达式输出的变量为控制器request.setAttribute(“参数名”，“值”)中的参数名字

4.输出对象的属性，使用方法

> 格式： ${对象名.属性名}

5.request.setAttribute中还可以传递对象，里面的参数值现在是一个对象

如果需要在el表达式中使用对象.属性名就需要这个属性中这个get方法才可以产生一个映射，如果一个对象中的属性存在但是没有他的get方法，那么在el表达式中是不能使用的

6.调用成员属性，属性名必须和类体定义的属性名相同，设置了get方法

## 扩展

1.request.setAttribute中传入的值的作用域是page级别的，所以当重定向的时候这个request的值会消失

2.session默认生命周期为30分钟或者关闭浏览器失效

# jdbc

## 项目中添加依赖

```java
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
</dependency>

```

![image-20230314100210524](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230314100210524.png)

导入数据库驱动pom的版本需要和数据库版本匹配

所以需要修改version为5.5.61之上

驱动导入之后，在项目中可以使用jar包中类

> 用于连接数据库，程序和数据库绑定在一起

## 连接数据库

使用结构connection创建连接

1.连接url

2.账号密码

3实例化connection接口

当实例化成功时，这个connection接口对象就和数据绑定一起了

连接url格式（5.1.+版本）

### 数据库驱动加载成功但是连接失败，可能是驱动版本的问题

jdbc:mysql://数据库电脑ip:访问数据库的端口/数据库名

导入的驱动的程序如果6.+url格式应给为 

```
jdbc:mysql://localhost:3306/cs2023?characterEncoding=UTF8&autoReconnect=true&useSSL=false&serverTimezone=UTC
```

## 执行数据操作

![image-20230314104415262](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230314104415262.png)

命名：多个单词使用下划线分割，其中不可以使用中文符号，通常使用字母小写

执行这个select时候需要使用st.executeQuery(sql),并且需要处理结果集 RsultSet

其他类型的语句使用st.executeUpdate(sql)来处理，返回处理的影响的数据表行数

## 释放连接

释放连接的目的：为其他线程空出连接的机会，连接的数量有限

```java
connection.close();
```

## 常见错误

1.数据库不能正常访问

​         数据库环境

​		数据库的驱动是否导入，==版本是否正确==

​		user and password 是否正确

​		==url是否正确==

2.执行数据操作失败

解决方法:将字符串中的sql语句复制到工具中执行

3.高版本驱动数据库8.x 

使用class.forname("com.mysql.jdbc.Driver")时候找不到，需要使用com.mysql.cj.jdbc.Driver

导入的驱动的程序如果6.+url格式应给为 

```
jdbc:mysql://localhost:3306/cs2023?characterEncoding=UTF8&autoReconnect=true&useSSL=false&serverTimezone=UTC
```

4.sql中字段类型和值匹配的问题

类型为varchar or date，值的界定符为一对单引号

![image-20230314110838614](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230314110838614.png)



# 关于项目mvc分层原因

## 浅析MVC分层架构的层次划分

[(638条消息) 浅析MVC分层架构的层次划分_今天也努力学习的Paul的博客-CSDN博客](https://blog.csdn.net/weixin_41192637/article/details/87871153)

## 为什么dao层和service层要用接口？

[(638条消息) 为什么dao层和service层要用接口？_为什么用service接口调用sql_夜路人的博客-CSDN博客](https://blog.csdn.net/wxljmy77/article/details/88415310)

使用接口便于后面修改代码，不至于牵扯后面的代码

==如果创建packgake,名字中间由-,会自动转为目录==

# 网页跳转思路

1.首先网页到达list展示的网页，

2.然后通过在list上点击按钮，跳转到相应的servlet的doGet方法中，在doGet中验证对应的对象id在数据库中存在之后，跳转到对应的add/update的网页中，

3.点击提交，跳转到对应的servlet中的doPost方法中校验数据是否正确，如果不正确，给出提示，正确，

4.再次跳转到list的页面，到这里，一个完整的功能完成

service层是用来完成对数据库的操作的

## 菜单的作用

把系统中所有的操作的servlet记录到数据表中

# statement和preparestatement的区别

![image-20230321103213078](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230321103213078.png)

1. Statement用于执行静态SQL语句，在执行时，必须指定一个事先准备好的SQL语句

　　2. PrepareStatement是预编译的SQL语句对象，语句中可以包含动态参数“?”，在执行时可以为“?”动态设置参数值

  3. PrepareStatement可以减少编译次数，提高数据库性能

     

# 如何解决输入输出问题



# 数据库

表中的字符串varchar最长不超过255个，但是如果要超过255个，需要使用数据类型longtext

# 图片处理

## CssSprites与Base64编码

简单陈述一下我对何时这使用这两种优化方法的看法。

- 使用CssSprites合并为一张大图：

  页面具有多种风格，需要换肤功能，可使用CssSprites
  网站已经趋于完美，不会再三天两头的改动（例如button大小、颜色等）
  使用时无需重复图形内容
  没有 Base64 编码成本，降低图片更新的维护难度。（但注意 Sprites 同时修改 css 和图片某些时候可能造成负担）
  不会增加 CSS 文件体积

- 使用base64直接把图片编码成字符串写入CSS文件：
  无额外请求
  对于极小或者极简单图片
  可像单独图片一样使用，比如背景图片重复使用等
  没有跨域问题，无需考虑缓存、文件头或者cookies问题

在引用外部样式文件时候尽量使用绝对路径

```
${pageContext.request.contextPath}/xx
```

# 多表查询

1.连接查询

select 字段 from 表2 left join 表1 条件 表1的某字段值与表某字段值相等

select 字段 from 表3 left join 表1 条件 表1的某字段值与表3字段值相等



查询角色id值等于2的用户权限

```sql
select m_url from sys_menu
left join sys_permission on
sys_menu.m_id =  sys_permission.m_id where r_id=2
```

2.条件查询

```sql
select m_url from sys_menu,sys_permission
where
sys_menu.m_id=sys_permission.m_id
and
r_id = 2
```

# jstl判断一个字符串是否包含另一个字符串

引入

```java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>
```

例子

```java
<c:forEach items="${page.list}" var="batchRecord" varStatus="index">
 
<c:if test="${fn:contains(batchRecord.sheetcode,'P')}">
<a href="xxx"> ${batchRecord.sheetcode} </a>
</c:if>
 
<c:if test="${fn:contains(batchRecord.sheetcode,'BL')}">
<a href="xxx">${batchRecord.sheetcode}</a>
</c:if>
```

# java程序实现通用sql查询

1.为啥要使用数据库

程序中的变量值由瞬时状态转换为持久状态（数据持久化）

2.java程序中变量值在程序运行结束之后，变量被释放，对应值消失

解决方法：把程序运行过程中一些重要的值记录下来保存在磁盘

操作数据库的步骤

1，加载驱动，连接数据库

2，执行sql语句

3.释放连接

## 问题提出

数据操作过程中，加载驱动，释放连接非核心业务，核心业务执行sql语句

考虑将非核心业务封装到一个工具类中（只需要传入sql语句直接得到结果）

1，减少重复语句

2.参数数量不确定如何解决

3.判断得到的结果

> 1,是否操作成功（update等）
>
> 2.提取数据，得到不同类型数据

## 问题解决

1.参数个数任意

形式

访问控制符 返回值类型 方法名（参数列表）

参数任意： 类型...  参数名 此时参数作为数组使用

![image-20230404101332948](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404101332948.png)

方法调用时候，第一个参数必须为String类型，第二个参数可以没有，由于object是所有java类的父类，后面参数可以是任意类型的

解决类方法中，要传入任意个参数形成完整的sql的问题

代码实现工具类

```java
package web.tools;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

public class DatabaseTool {
    /**
     * 通用增删改
     * @param sql
     * @param args
     * @return
     */

    public static boolean executeSQL(String sql,Object... args){
        boolean result = false;
        try{
            Class.forName(SystemConstant.DRIVER);
            Connection connection = DriverManager.getConnection(SystemConstant.URL,SystemConstant.USER,SystemConstant.PASSWORD);
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            //            String sql = "update sys_menu set m_title=?,m_url=?,m_ico=?,m_type=?,m_order=?,m_parent=? where m_id=?";
            for(int i = 0;i<args.length;i++){
                preparedStatement.setObject(i+1,args[i]);
            }


            if(preparedStatement.executeUpdate()>0){
                result = true;
            }
            preparedStatement.close();
            connection.close();

        }catch (Exception e){
            e.printStackTrace();
        }
        return result;
    }
}

```

![image-20230404104106619](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404104106619.png)



最后是实现了通用的增删改的方法的封装

# 数据提取

数据提取分两种情况

1。提取数量，根据统计查询除的结果实现分页

```java
public static int getCount(String sql,Object... args){
        int count =0;

        try{
            Class.forName(SystemConstant.DRIVER);
            Connection connection = DriverManager.getConnection(SystemConstant.URL,SystemConstant.USER,SystemConstant.PASSWORD);
            PreparedStatement preparedStatement = connection.prepareStatement(sql);

            if(args==null||args.length<1){

            }else{
                for(int i = 0;i<args.length;i++){
                    preparedStatement.setObject(i+1,args[i]);
                }
            }

            ResultSet resultSet = preparedStatement.executeQuery();
            if(resultSet.next()){
                count  =resultSet.getInt(1);
            }

            preparedStatement.close();
            connection.close();
        }catch (Exception e){}


        return count;
    }
```

注意：传入的sql语句为统计的查询形式： select count(字段) from 表名 where 字段=？

# 如何形成不同的类对象

查询之后，根据不同的数据表，返回不同的类对象

解决： 类的反射+泛型

反射：通过程序去查找这个类有哪些属性，通过程序创建对象

根据不同的类，成员属性不相同，如何解决程序实现对成员属性的赋值

如何通过这个工具类实现类对象的自动装配  等同  spring自动装配的底层原理

### 使用反射机制解开类定义中的成员属性，再使用程序装配值给成员

![image-20230404173336614](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404173336614.png)

使用反射，给存在的成员属性注入值

![image-20230404174256293](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404174256293.png)

将==object==的==name==属性设置为==”张三“==

## 自动装配

![image-20230404175331302](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404175331302.png)

这里需要通过设计java程序来提取resultset里面的列名

![image-20230404182518712](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404182518712.png)

得到查询之后的列名称

通过==列名称--反射--类对象成员属性值==

前提：查询结果的==列名称和成员属性名相同==，这样才可以通过列名称找到类对应的属性，并且通过查找出来的值来给对应属性赋值

如果不相同，那么这个反射则会==失败==

解决办法：如果查询出来的一个javabean对应的表的字段名和类的成员属性名字不完全相同，那么需要在sql语句中给需要查询的字段==别名，让别名和这个类的成员属性名字对应上==

==最终目的：通过提取列的名称，反射找到类的成员属性，外部给成员属性赋值，最后形成java对象，就不用自己在query时候去不断的set赋值，简化开发==

最后需要实现通用的查询形成不同的java对象，在设计sql时候，别名需要和类的成员属性名相同

```java
/**
     * 返回一个完整的对象，有实例，成员属性有参数,针对这个query的sql语句
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T getEntity(Class<T> clazz,String sql,Object... args){
        T object = null;
        try {

            Class.forName(SystemConstant.DRIVER);
            Connection connection = DriverManager.getConnection(SystemConstant.URL,SystemConstant.USER,SystemConstant.PASSWORD);
            PreparedStatement preparedStatement = connection.prepareStatement(sql);

            //通过一个任意参数的方式来给？赋值
            for(int i = 0;i<args.length;i++){
                preparedStatement.setObject(i+1,args[i]);
            }
            
            
            ResultSet resultSet = preparedStatement.executeQuery();

            ResultSetMetaData metaData  = resultSet.getMetaData();//获取查询的表的字段名

            //如果数据查询有结果，才去实例化对象
            if(resultSet.next()){
                object = clazz.newInstance();
                for(int i = 0;i<metaData.getColumnCount();i++){
                    //获取每个字段名字，并且获取该字段值（前提的准备条件）
                    String columnName = metaData.getColumnLabel(i+1);
                    Object columnValue=resultSet.getObject(i+1);

                    //准备开始注入值
                    Field field = clazz.getDeclaredField(columnName);//通过列的名称查找类的成员属性
                    field.setAccessible(true);//设置外部可访问  public
                    field.set(object,columnValue);//给该object对象对应的成员属性赋值
                }
            }




            resultSet.close();
            preparedStatement.close();
            connection.close();
            //通过反射获取类的成员属性
////            Field[] fields = clazz.getDeclaredFields();
//
//
//            Field f1 = clazz.getDeclaredField("name");//找到是否存在该成员属性
//            f1.setAccessible(true);//设置成员属性可以被外部访问
//            f1.set(object,"张三");//外部设置成员属性值


        }catch (Exception e){}
        return object;
    }
```

==上面是一个单个对象的万能查询，需要注意的是，给数据表的字段一个别名（这个别名和类成员属性需要相同）==，所以这里的==sql语句的书写需要注意==，（以上是对于一个对象来做的）

总结：我们最后要做出来的东西是，对象的类型有我们来指定，sql查询语句需要我么来书写，sql中的？,参数由我么来通过一个任意参数的方式来设定，sql语句查询出来结果之后，通过获取这个表的字段名来反射查找对应类的成员属性是否存在，如果存在，通过获取你写的sql语句条件下的一行值，最后通过你刚查询出来的表中的值来给你指定的对象的成员属性赋值，最后可以获得一个你要的对象类型，已经给其赋值，需要注意的是sql语句的书写，书写时候的别名要和成员对象名字相同，否则无法成功反射，也就无法给成员属性赋值，并且以上是对于查找一个对象的方法

****



如果成员属性名和数据库保留字也就是关键字冲突，别名需要通过mysql转义符号==``==,保险起见，可以将别名都加上转义字符

![image-20230404232652025](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230404232652025.png)

针对==多个对象==的自动实例化以及值得自动注入

```java
/**
     * 返回多个完整的对象，有实例，成员属性有参数,针对这个query的sql语句
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> List<T> getListEntity(Class<T> clazz, String sql, Object... args){
        List<T> objects = new ArrayList<>();
        try {

            Class.forName(SystemConstant.DRIVER);
            Connection connection = DriverManager.getConnection(SystemConstant.URL,SystemConstant.USER,SystemConstant.PASSWORD);
            PreparedStatement preparedStatement = connection.prepareStatement(sql);

            //通过一个任意参数的方式来给？赋值
            if(args==null||args.length<1){

            }else{
                for(int i = 0;i<args.length;i++){
                    preparedStatement.setObject(i+1,args[i]);
                }
            }


            ResultSet resultSet = preparedStatement.executeQuery();

            ResultSetMetaData metaData  = resultSet.getMetaData();//获取查询的表的字段名


            T object = null;
            //如果数据查询有结果，才去实例化对象
            while(resultSet.next()){
                object = clazz.newInstance();
                for(int i = 0;i<metaData.getColumnCount();i++){
                    //获取每个字段名字，并且获取该字段值（前提的准备条件）
                    String columnName = metaData.getColumnLabel(i+1);
                    Object columnValue=resultSet.getObject(i+1);

                    //准备开始注入值
                    Field field = clazz.getDeclaredField(columnName);//通过列的名称查找类的成员属性
                    field.setAccessible(true);//设置外部可访问  public
                    field.set(object,columnValue);//给该object对象对应的成员属性赋值
                }
                objects.add(object);
            }




            resultSet.close();
            preparedStatement.close();
            connection.close();


        }catch (Exception e){
            e.printStackTrace();
        }
        return  objects;
    }
```

