---
layout: post
title: 关于项目移植过程中，如何在不修改java源程序的情况下，如何适应新环境下的mysql
date: 2023-08-11 09:00:00 +0800
category: Servlet
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/servlet.jpg
icon: code
---


* content
{:toc}
# 关于项目移植过程中，如何在不修改java源程序的情况下，如何适应新环境下的mysql

## 解决这个问题的原因：

在我们将这个自己电脑里面写好的web项目部署到这个我们自己的云服务器之前，我们已经部署好了这个相应的环境，jdk,tomcat ,mysql ,如果我们只是将自己的web项目部署到这个tomcat下面的话，这个数据库很可能是连接不是上的，

==原因是因为这个原来项目中的数据源的配置信息或者是这个数据库的连接信息，也就是自己电脑上面的程序工具类中的driverclass，url，password等数据库连接信息和云服务器上部署数据库不相同，==

但是如果我们又回到自己电脑修改源代码，在删除云服务器上之前部署的原来的web项目，再启动，非常麻烦，所有我们需要使用一个方法，他要达到的目的是：

==只通过修改记事本中的一些对应的连接信息，运行程序之后，程序自动读取这个记事本，==换句话说，==我们要将源程序中的写死的信息，改变为我们认为的输入数据库连接信息或者数据源配置信息。==

结合以上原因以及我们最后所要达到的目的，我们对点餐系统进行具体的操作

### 我们改变之前的c3p0用xml文件配置的方式（这里可以自己百度了解下c3p0的三种实现方式）为java代码的方式

1.删除之前的xml文件（记得自己要==保存==起来）

2.创建一个properties文件在resource文件下

不同的数据源以及数据库的配置信息有不同的写法，具体写法可以写项目时候百度copy

> propeties文件的作用 通过kv的方式来储存这个数据库的连接信息以及数据源的配置信息，所以下图中根据具体的名字来填写你要配置的信息尽量在名字前面加上前缀便于区分

![image-20230402165828299](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402165828299.png)



3.接下来创建一个java代码来根据对应的名字获取对应的值来配置c3p0

这个配置文件必须这样命名且必须放在resourecs下面

![image-20230402203620311](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402203620311.png)

配置文件里面必须这样写

```xml
c3p0.driverClass=com.mysql.jdbc.Driver
c3p0.jdbcUrl=jdbc:mysql://127.0.0.1:3306/hotel?useUnicode=true&characterEncoding=utf8&useSSL=false&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=Asia/Shanghai
c3p0.user=root
c3p0.password=root
c3p0.maxPoolSize = 20
c3p0.minPoolSize = 3
c3p0.maxStatements = 30
c3p0.maxIdleTime = 150
```

v值自己修改，但是k值都要有并且开头要用c3p0,这里关乎后面的映射的问题，非常重要

4.写一个工具类

```java
public class c3p0Utils {
    private static ComboPooledDataSource dataSource;

    public static Connection init() {
        try {
            //创建c3p0类
            dataSource = new ComboPooledDataSource();

            //创建连接
            Connection conn = dataSource.getConnection();
            return conn;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

这里的conn或自动对每个参数进行映射，如果缺少一个，就会出现问题

5.最后对自己的dao层实现类进行修改

![image-20230402204341060](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402204341060.png)

![image-20230402204417574](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402204417574.png)

最后的的数据也是成功的出来了

![image-20230402204449389](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402204449389.png)

## 结论：这个问题的解决可以简化一些操作，让数据库的连接信息或者是数据源的配置信息不再死写在java源码中，修改信息更加便捷

