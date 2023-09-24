---
layout: post
title: 微信公众号开发
date: 2023-09-10 9:42:00 +0800
category: Weixin
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/set.jpg
icon: note
---


* content
{:toc}
# 微信公众号开发

## 1.配置接口信息
### 配置url
首先，大家尽量将tomcat默认port改为80

如何修改
[点击链接](https://blog.csdn.net/qq_47354826/article/details/116896514#:~:text=1%20%E6%89%BE%E5%88%B0tomcat%E7%9B%AE%E5%BD%95%2Fconf%2Fserver.xml%202%20%E9%80%89%E6%8B%A9%E4%BB%A5%E8%AE%B0%E4%BA%8B%E6%9C%AC%E6%89%93%E5%BC%80%EF%BC%8C%E6%8A%8A8080%E6%94%B9%E4%B8%BA%E4%BD%A0%E6%83%B3%E4%BF%AE%E6%94%B9%E7%9A%84%E7%AB%AF%E5%8F%A3%E5%8F%B7%EF%BC%8C%E8%BF%99%E9%87%8C%E9%80%89%E6%8B%A9%E4%BF%AE%E6%94%B9%E4%B8%BA8%EF%BC%8C%E4%BF%AE%E6%94%B9%E5%90%8E%E4%BF%9D%E5%AD%98%20%E6%B3%A8%E6%84%8F%EF%BC%9A%201%EF%BC%89%E4%BF%AE%E6%94%B9%E7%9A%84%E7%AB%AF%E5%8F%A3%E4%B8%80%E5%AE%9A%E4%B8%8D%E8%83%BD%E8%A2%AB%E5%8D%A0%E7%94%A8,2%EF%BC%89%E4%BF%AE%E6%94%B9%E5%AE%8C%E6%88%90%E5%90%8E%EF%BC%8C%E8%BF%9B%E5%85%A5bin%E7%9B%AE%E5%BD%95%EF%BC%8C%E5%85%88%E5%90%AF%E5%8A%A8shutdown.bat%EF%BC%8C%E5%86%8D%E5%90%AF%E5%8A%A8startup.bat%20%E9%87%8D%E5%90%AFtomcat%E6%9C%8D%E5%8A%A1%E5%99%A8%203%20%E6%89%93%E5%BC%80tomcat%E5%90%8E%EF%BC%8C%E6%89%93%E5%BC%80%E6%B5%8F%E8%A7%88%E5%99%A8%E8%BE%93%E5%85%A5%20http%3A%2F%2F%40localhost%3A8%20%E7%9C%8B%E5%88%B0%E5%B0%8F%E7%8C%AB%EF%BC%8C%E4%BF%AE%E6%94%B9%E7%AB%AF%E5%8F%A3%E6%88%90%E5%8A%9F%EF%BC%81)

原因：我们的域名和我们的ip是对应上的，如果接口配置中想要不加port，就需要将端口改为80，这样访问域名时才会自动加上port 80,包括项目的启动端口，采用tomcat默认端口80

2.域名后紧跟自己的项目名字
url格式：http://你的域名/你的项目启动名/init

这里init对应controller中的requestmapping中的vale=init

配置信息结束

## 2.刷新token

beans.xml文件

代码头部，下面加不了
><?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"



```

       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 1：定义任务的bean ，这里使用JobDetailFactoryBean,也可以使用MethodInvokingJobDetailFactoryBean ，配置类似-->
    <bean name="hwJob" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
        <!-- 指定job的名称 -->
        <property name="name" value="hw_job"/>
        <!-- 指定job的分组 -->
        <property name="group" value="hw_group"/>
        <!-- 指定具体的job类 -->
        <property name="jobClass" value="com.zwf.quartz.J1"/>
        <!-- 必须设置为true，如果为false，当没有活动的触发器与之关联时会在调度器中会删除该任务  -->
        <property name="durability" value="true"/>
        <!-- 指定spring容器的key，如果不设定在job中的jobmap中是获取不到spring容器的 -->
        <property name="applicationContextJobDataKey" value="applicationContext"/>
    </bean>
    <!-- 2.1：定义触发器的bean，定义一个Simple的Trigger，一个触发器只能和一个任务进行绑定 -->
    <bean name="simpleTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean">
<!--        指定Trigger的名称-->
        <property name="name" value="hw_trigger"/>
<!--        指定Trigger的名称-->
        <property name="group" value="hw_trigger_group"/>
<!--        指定Tirgger绑定的Job-->
        <property name="jobDetail" ref="hwJob"/>
<!--        指定Trigger的延迟时间 1s后运行-->
        <property name="startDelay" value="1000"/>
<!--        指定Trigger的重复间隔  5s-->
        <property name="repeatInterval" value="6000000"/>
<!--        指定Trigger的重复次数-->
        <property name="repeatCount" value="5"/>
    </bean>

    <!-- 2.2：定义触发器的bean，定义一个Cron的Trigger，一个触发器只能和一个任务进行绑定 -->
    <bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
        <!-- 指定Trigger的名称 -->
        <property name="name" value="hw_trigger"/>
        <!-- 指定Trigger的名称 -->
        <property name="group" value="hw_trigger_group"/>
        <!-- 指定Tirgger绑定的Job -->
        <property name="jobDetail" ref="hwJob"/>
        <!-- 指定Cron 的表达式 ，当前是每隔1s运行一次 -->
        <property name="cronExpression" value="0/5 * * * * ?" />
    </bean>


    <!-- 3.定义调度器，并将Trigger注册到调度器中 -->
    <bean id="scheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="triggers">
            <list>
                <!-- <ref bean="simpleTrigger"/> -->
                <ref bean="cronTrigger"/>
            </list>
        </property>
        <!-- <property name="autoStartup" value="true" /> -->
    </bean>

<!--    <bean name="runMeJob" class="org.springframework.scheduling.quartz.JobDetailBean">-->

<!--        <property name="jobClass" value="com.zwf.quartz.WeixinQuartzJob" />-->

<!--        <property name="jobDataAsMap">-->
<!--            <map>-->
<!--                <entry key="refreshAccessTokenTask" value-ref="refreshAccessTokenTask" />-->
<!--            </map>-->
<!--        </property>-->

<!--    </bean>-->

<!--    <bean id="simpleTrigger"-->
<!--          class="org.springframework.scheduling.quartz.SimpleTriggerBean">-->

<!--        <property name="jobDetail" ref="runMeJob" />-->
<!--        <property name="repeatInterval" value="5000" />-->
<!--        <property name="startDelay" value="1000" />-->

<!--    </bean>-->

<!--    <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">-->
<!--        <property name="jobDetails">-->
<!--            <list>-->
<!--                <ref bean="runMeJob" />-->
<!--            </list>-->
<!--        </property>-->

<!--        <property name="triggers">-->
<!--            <list>-->
<!--                <ref bean="simpleTrigger" />-->
<!--            </list>-->
<!--        </property>-->
<!--    </bean>-->


<!--    <bean id="refreshAccessTokenTask"/>-->
</beans>
```

beanx.xml文件中的J1

```
public class J1 implements Job {

    public void execute(JobExecutionContext context)
            throws JobExecutionException
    {
        System.out.println("runing====");

        RefreshAccessTokenTask.refreshToken();
    }
}
```


jsonUtils工具

```
import cn.hutool.core.lang.TypeReference;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.slf4j.LoggerFactory;

import java.io.IOException;
import java.util.logging.Logger;

public class JsonUtils {
//    private static Logger logger = (Logger) LoggerFactory.getLogger(JsonUtils.class);
    private static ObjectMapper objectMapper = new ObjectMapper();
    static {
        //静默出现未知属性时的异常
        objectMapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
        //允许json=""的空字符换入参
        objectMapper.enable(DeserializationFeature.ACCEPT_EMPTY_STRING_AS_NULL_OBJECT);
    }

    public static String toJson(Object object) {
        try {
            return objectMapper.writeValueAsString(object);
        } catch (Exception e) {
//            logger.log(e.getMessage(), e);
            e.printStackTrace();
        }
        return null;
    }

    public static <T> T fromJson(String json, Class<T> clazz) {
        try {
            return (T)objectMapper.readValue(json, clazz);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

//    public static <T> T fromJson(String json, TypeReference<T> type){
//        try {
////            return (T)objectMapper.readValue(json, type);
//        } catch (IOException e) {
////            logger.error(e.getMessage(), e);
//            e.printStackTrace();
//        }
//        return null;
//    }
}
```

具体的导包有
[点击查看](https://img-blog.csdnimg.cn/62db2d48ab9e446c865cbd0dd351d37e.png)

导入依赖

```
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.6</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.6</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.6</version>
    </dependency>
```

### 2.refreshAccessTokenTask视频中爆红问题
由于component对象注入问题，refreshAccessTokenTask对象为null,通过上面我在网上找的beans.xml文件

注意：我这里将视频中的refreshToken方法改为static方法，方便调用，可直接越过对象注入问题，但只是为了开发方便，具体容器component对象注入问题请自行了解解决

### 3.45009调用接口次数问题

access_token每日获取token限制次数为20次





参考
[https://www.cnblogs.com/mjbenkyo/p/12146972.html](https://www.cnblogs.com/mjbenkyo/p/12146972.html)

以及官方文档
[https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/getStableAccessToken.html#%E8%B0%83%E7%94%A8%E7%A4%BA%E4%BE%8B](https://developers.weixin.qq.com/doc/offiaccount/Basic_Information/getStableAccessToken.html#%E8%B0%83%E7%94%A8%E7%A4%BA%E4%BE%8B)




## 请求
所有从公众号点击之后发起的请求都会发给配置好的url中，所有需要写一个处理发过来的请求的方法

## 被动消息
用户发过来某个特定的消息之后，后台回复消息，不能主动推送消息，后台可根据消息的不同类型类确定返回值

## 模板消息
可以主动和关注用户推送消息

## 消息发重复的原因
消息服务器在5s内收不到响应，会重新发请求，一共三次

推荐使用FromUserName+CreateTime排重



## FileUtil

```
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;

public class FileUtil {

    public static void inputstream2file(InputStream inputStream,File file){
        if(inputStream!=null&&file!=null){
            try {
                java.nio.file.Files.copy(
                        inputStream,
                        file.toPath(),
                        StandardCopyOption.REPLACE_EXISTING);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 关于map2xml时的转义问题
[点击查看](https://img-blog.csdnimg.cn/c4acf5377c0e40fb83023898292f0731.png)
简单来说setEscaptext方法默认时开启转义的，所以需要设置为false

可以查看dom4j官方文档
[https://www.oschina.net/uploads/doc/dom4j-1.6.1/index.html](https://www.oschina.net/uploads/doc/dom4j-1.6.1/index.html)

## mybatis不用mapper注解原因

配置文件中如果已经设置了扫描的路径，就不用加上注解@mapper

## 网页授权
![](https://img-blog.csdnimg.cn/699ed4b2dc1946529668731b3ec70f62.png)

如果通过微信客户端打开的user-agent应该为micromessenger,这里第一次发送请求过去获取到openid过后会重新带上state参数去再次发请求

## 未解决问题
在kit工具包中使用service  or  mapper 去操作数据库，总是会出现nullpointer的情况，已排除扫描包的问题，初步认为是使用位置的问题，在正常的controller中使用是没有问题的


## 微信公众号用户与网站用户的绑定的原因以及解决方案分析

对于同一个用户，我们需要建立微信公众号用户openid 和网站用户userid之间的对应关系

这里需要清楚在数据库中的user表，openid表示的字段是一个唯一的微信用户，不同的openid是不同的微信用户，数据库中不仅存储了微信用户的名字在数据库中，对应的还有网站用户的username and passwd

如果一个微信用户进入网站之后没有绑定用户的话，在数据库只有微信用户的数据，对应的网站数据的username and passwd 是空的

情况1.绑定已经在数据库中的用户关系，需要新加入一条数据库数据，删除原来的

绑定用户的逻辑：

1.首先如果当前微信用户如果已经授权，那么之间跳到首页

2.如果没有授权，那么分以下两种情况
> 1.绑定之前还没有绑定过的用户，就直接查出数据库中当前微信用户的数据，update就可以
> 
> 2.绑定之前绑定过的用户，可以通过重新建立一个对象，insert，最后将之前的数据中的数据删除就可以


[https://cloud.tencent.com/developer/article/1724915](https://cloud.tencent.com/developer/article/1724915)

## 微信二维码
对象实例中得qrData一般用户特殊得业务需求来携带不同得参数，比如 扫描得微信用户得用户信息（openid）  or 用户得分组信息（groupid）

二维码扫描得逻辑： [https://cloud.tencent.com/developer/article/2245869](https://cloud.tencent.com/developer/article/2245869)

在通过带tickt得方式生成了二维码之后，微信用户扫描二维码，如果没有关注，跳到关注页面，如果关注了跳到公众号页面，这些都是微信内部做得，但是此时这个微信客户端会向我们得服务发送xml,我们通过获取到不同得xml得内容，来做不同得数据操作，这时候微信客户端得看上去只是跳转到关注公众号或者 公众号内部


处理消息：每个公众号都有一个数字，前面1-1000个数字是永久二维码，后面得是临时二维码，扫描二维码发过来得xml中存在这个数据，可以通过该数据去数据库中查找具体二维码对应得操作

## 扫码登录

流程：当扫码登录得二维码一直显示二维码时候，没有人去扫描二维码得时候，前端一直向登录得url请求，后台会一直打印，直到前台打印20之后，显示二维码过期，需要重新刷新页面，respons.getwriter().println("特定得值"),当微信用户扫码完成之后，对数据库进行一定操作，向后台打印特定值，前端判定完成扫码之后，停止发请求，直接跳转到首页

## 云服务器太卡得解决方法
这里可以使用自己得电脑来进行开发，太卡会影响开发效率，最后可以把成品项目放服务器上面去调试，我们采用内网穿透得方法，将我们自己得本地tomcat映射到ip上，下面是内网穿透在微信服务器和我们后台服务器得关系

![](https://img-blog.csdnimg.cn/0df6d56c882c4650acdc11cc057b6c1c.png)


使用natapp进行内网穿透，软件下载地址[https://natapp.cn/](https://natapp.cn/)

使用教程[https://natapp.cn/article/natapp_newbie](https://natapp.cn/article/natapp_newbie)

# 总结
微信公众号开发相当于前端定义好了规则，点击一些按钮或者发送一些消息之后，全部会通过post的方式发给配置好的url，在后台进一步处理

微信公众号开发更注重于在我们在微信公众号点击菜单之后，微信向后台服务器发送xml，这里的xml处理是比较重要的，以及在判断xml消息类型之后我们根据不同的消息类型来做不同的消息返回处理，这里更重要的是学习如何去阅读微信公众号的官方文档

当我们绑定完用户，跳转到自己的网站的首页之后，就可以继续开发你的网站了，这里只比之前的网站多一个绑定用户的页面，这个页面应该相当于登录注册的页面了

所有在微信公众号得一些操作，会通过xml消息得方式发送回服务器后端，包括关注操作，取消关注操作

