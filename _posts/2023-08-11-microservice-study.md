---
layout: post
title: 微服务学习
date: 2023-08-11 09:12:00 +0800
category: Mircoservice
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/microservice.jpg
icon: note
---


* content
{:toc}
# 微服务学习

## 写一个服务的步骤一般可以有

1，在pom.xml文件中导入相关的依赖

2，编写相关的配置类（application.yml）

​	配置类编写的三步

​	1.服务的端口

​	2.服务的名字

​	3.服务的地址信息

3.启动相关的功能注解（@EnableXXXX）



## 单体结构（适合小型项目 学生管理系统）

也就是平常学校里面的作品，功能都放在一个项目里面，有功能就直接添加，缺点是耦合度高，修改一处的，一个项目可能就崩掉了

## 分布式架构（适合大型项目 京东，淘宝）

降低服务耦合度，有利于服务升级拓展

## 服务拆分

不同的微服务，不要重复开发相同的业务

微服务可以将自己的业务暴露为接口，暴露给其他服务使用

不同的服务应该有自己的独立的数据库

注意当spring-boot-maven-plugin爆红，可以通过清理缓存

> File -> [Invalidate](https://so.csdn.net/so/search?q=Invalidate&spm=1001.2101.3001.7020) Caches

## 微服务远程调用

> 通过一个服务对另一个服务的发起请求，就可以完成远程调用

利用RestTemplate 发起远程调用

## 消费者和提供者

消费者：调用其他服务的一方

提供者:被调用的一方

一个服务可以是消费者也可以是提供者，这是相对的一个概念

## eureka注册中心

对于注册中心来说，所有的这个需要注册进去的都属于这个eureka-client客户端 ,eureka在启动的时候也会将自身注册到注册中心里面，它也是一种服务

![image-20230108180103669](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108180103669.png)

![image-20230108180132835](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108180132835.png)

## 对于SpringBoot启动启动项目出现o.s.b.d.LoggingFailureAnalysisReporter 错误解决方法

把@SpringBootApplication改成 @SpringBootApplication(exclude={DataSourceAutoConfiguration.class, HibernateJpaAutoConfiguration.class})

一定要注意这个yaml文件的缩进问题，如果有一个缩进的地方不对都会导致项目运行失败或达不到自己想要的结果，少写一个字母都不行

## 服务注册

1.导入eureka-client依赖

2.编写application.yml文件配置eureka地址

## 服务发现

1.修改发起的请求，不用端口，用服务名请求

2，在获取RestTemplate的方法上加个@LoadBanlanced做负载均衡

## 总结

![image-20230109155900630](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109155900630.png)

## 负载均衡

![image-20230109161931299](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109161931299.png)

第一种作用在所有服务中

第二种修改userservice可以指定一种特定的服务

## 饥饿加载

Ribbon 在进行客户端负载均衡的时候并不是在启动时就加载上下文，而是在==实际请求的时候才去创建==，因此这个特性往往会让我们的第一次调用显得颇为疲软乏力，严重的时候会引起==调用超时==。所以我们可以通过指定 Ribbon 具体的客户端的名称来开启==饥饿加载==，即在==启动的时候便加载所有配置项的应用程序上下文==
原文链接：https://blog.csdn.net/wo18237095579/article/details/83418695

![image-20230109163101690](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109163101690.png)

Nacos 

也是一个类似于eureka的东西，但是功能更加丰富一些，由阿里巴巴开发出来的

记得单击startup.cmd的时候默认是通过集群模式启动的，这种是linux里面启动用的，但是windows里面一般使用单机模式，所以需要用记事本打开startup.cmd里面的set mode="standalone"

![image-20230109171015515](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109171015515.png)



如果改为使用nacos的话，服务提供者和消费者不用改变

改变有：

1.导入的依赖

2.注册中心的地址改为nacos的地址

nacos的默认登录账号和密码都是nacos

## Nacos

### nacos注册中心

![image-20230109175017282](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109175017282.png)

nacos下面包含地域集群，集群下面包含多个实例

![image-20230110213706942](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110213706942.png)

### Nacos负载均衡

我们希望这个服务在远程调用的时候，首先调用这个本地服务，在本地服务出现问题的时候调用其它地域的服务集群 

![image-20230110215428332](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110215428332.png)

### Nacos加权负载均衡

![image-20230110221204395](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110221204395.png)

### Nacos环境隔离

namespasce是专门做这个的，一般用来做那个test测试环境和dev运行环境的隔离，和这个服务下面那个地域集群和再下面的实例不一样，后者可以相互访问，前者两两之间不可以相互访问

![image-20230110223548816](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110223548816.png)

### Nacos和Eureka的区别和共同点

![image-20230110224353108](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110224353108.png)

### Nacos的配置管理

==最后需要实现服务的热更新==

![image-20230110225908713](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110225908713.png)

只配置需要热更新的部分开关，这个Nacos的配置管理有点像springclud-config

![image-20230111182845586](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111182845586.png)



### bootstrap.yml文件

这个文件的等级是项目级别的，所以会在项目启动之后先来扫描它了，这样可以将nacos的地址和nacos配置中心的配置文件名字放在里面

![image-20230111182452493](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111182452493.png)

### nacos配置的热更新

![image-20230111184543122](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111184543122.png)

### 多服务共享配置及其优先级

![image-20230111185743714](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111185743714.png)

![image-20230111185822604](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111185822604.png)

### Nacos集群搭建

![image-20230111203030904](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111203030904.png)

##  Feign

> 一种http声明式的客户端，帮助我们优雅的实现http请求的发送，替代resttemplate远程调用的方式
>
> feign可以用来做http请求发送的维护，也集成了ribbon的负载均衡功能

解决resttmpelate远程调用服务难以维护的问题，这种方式可以让spring帮助我们维护http请求，只要我们提供相应的地址信息

![image-20230111211932904](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111211932904.png)

记得这个不同环境的服务是没法调用的

### feign的日志加载

![image-20230112195043620](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112195043620.png)

### Feign优化

日记级别有none basic  headers full 最常用的就是basic,其次是full

对于这个feign的底层客户端的实现，一般来说，默认采用的是urlconnection,这种没有连接池，速度会慢点，

如果要优化feign,直接用httpclient（这种有连接池，速度快点）替代这个默认的urlconnction

![image-20230112201148194](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112201148194.png)

### feign最佳实践

![image-20230112202233006](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112202233006.png)

不同包的feignclient的导入方式

![image-20230112203241448](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112203241448.png)

用feign-api的方式来抽取代码是非常好用的，一方面抽取为一个模块之后，导入的openfeign依赖可以帮助我们实现远程服务调用，还集成了ribbon做负载均衡

## Gateway网关

### 服务路由

>  通过gateway网关的时候，请求需要网关来转发，转发到哪个服务，就是我们的服务路由

### 负载均衡

> 得到了服务的名字，通过路由直到转发到哪个服务，我们还需要在同一个服务名字的多个实例中做个挑选，这里就是负载均衡

![image-20230112204605617](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112204605617.png)

### 搭建网关服务

![image-20230112214448302](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112214448302.png)

### predicate断言工厂

> 利用这种模式，可以给路由断言加一些条件，比如时间或者从哪个位置发过来的ip

![image-20230112215403815](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112215403815.png)

### 过滤器工厂gatewayfilter

![image-20230113201444392](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113201444392.png)

### 全局过滤器

![image-20230113202328245](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113202328245.png)

![image-20230113205410189](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113205410189.png)

但是声明的顺序是从1逐级递增的

### 跨域问题

利用cors解决这个跨域的问题，做一些application.yml的配置就可以了

什么是跨域： 

1.域名不相同，例如www.baidu.com和www.xxxx.com 

2.域名相同但是端口不同 ，例如local host:8080和localhost:8081

## 异步同步通讯

事务场景

![image-20230113220916901](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113220916901.png)



### 同步通讯问题

![image-20230113213016283](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113213016283.png)

### 异步通讯

> 用于解决同步通讯问题

![image-20230113220640498](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113220640498.png)

主要依赖broker这个组件做个缓存

























