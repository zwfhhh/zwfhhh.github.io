---
layout: post
title: 关于mybatis中的相关问题
date: 2023-08-11 08:30:00 +0800
category: Mybatis
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/mybatis.jpg
icon: note
---


* content
{:toc}
# 关于mybatis中配置文件属性的书写顺序的问题

        configuration（配置）
    properties（属性）
    settings（设置）
    typeAliases（类型别名）
    typeHandlers（类型处理器）
    objectFactory（对象工厂）
    plugins（插件）
    environments（环境配置）
    	environment（环境变量）
    		transactionManager（事务管理器）
    		dataSource（数据源）
    databaseIdProvider（数据库厂商标识）
    mappers（映射器）
***
# 关于resultType 和 resultMap 的区别问题
## resultType中只是写的映射关系中的对应实体类名  
## resultMap  ：设置自定义的映射关系  
id：唯一标识  
type : 处理映射关系的实体类的类型  
常用标签：  
id:处理主键和实体类中属性的映射关系  
result:处理普通字段和实体类中属性的映射关系 
association:处理多对一的映射关系（处理实体类类型的属性）  
### collection:处理一对多的映射关系（处理集合类型的属性）
ofType:设置集合中属性中存储的数据类型  
****
column:设置映射关系中的字段名，必须是sql查询出的一个字段  
property:设置映射关系中的属性的属性名，必须是处理的实体类类型中的属性名  
# 关于数据库中的字段名和javabean实体类中的属性名不完全相同  
   
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>  
将这个数据库中的字段名带下划线的直接变为驼峰的，当字段名为emp_id对应的JavaBean对象属性名为empId  
# 关于数据库中多表联查的情况（无法在对应的实体类中形成对应的映射，名字不相同，数据类型也不同）
![模板](https://img-blog.csdnimg.cn/1f2dce2ee39245ce893bd72f6f49e3c0.jpeg)


# Mybatis中#{}和${}的用法（里面的数据一般是从接口中的对象属性获得的，里面的名字和对象属性的名字一样，有对应关系的）

## 1、#{}将传入的数据当作一个字符串，会对传入的数据加上一个双引号。比如，select * from student where student_name = #{studentName}如果传入的值为xiaoming，那么解析成sql的值为student_name="xiaoming"。

## 2、${}将传入的数据直接显示生成在sql中。如 :select ${fieldNmae} from student where student_age = 18 此时，传入的参数作为要查询的字段，如果传入的值为student_name，则解析成的sql为:select student_name from student where age = 18
## 3、#{}方式能够很大程度上防止sql注入。

## 4、${}无法防止sql注入。

## 5、${}方式一般用于传入数据库对象，例如列表和表名。

## 6、由于#{}方式具有更高的安全行，所以能用#{}的地方尽量不要使用${}。

## 7、Mybatis排序时使用order by动态参数时需要注意，用${}而不是#{}。动态sql时mybatis的主要特性之一，在mapper中定义的参数传到xml中之后，在查询之前mybatis会对其进行动态解析。mybatis提供了两种支持动态sql的语法：#{}以及${}。比如，下面两个语句，如果参数name值为:xioming，则这两种方式无任何区别;select * from student where name = #{name}select * from student where name = ${name} 其解析之后的结果均为：select * from student where name = "xiaoming"
# {}和${}的不同之处在于预编译中的处理。#{}在预处理时，会把参数部分用一个占位符?代替，变成如下的sql语句：

## select * from student where name = ?
## 而${}只是简单的字符串替换，在动态解析阶段，该sql语句会被解析成：

## select * from student where name = "xiaomng"
# {}的参数替换发生在DBMS中，而${}则发生在动态解析过程中。

 
