---
layout: post
title: springmvc 控制器接收数据 检验数据 以及输出数据
date: 2023-09-13 11:30:00 +0800
category: SpringMvc
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/springmvc.jpg
icon: note
---


* content
{:toc}
# springmvc 控制器接收数据 检验数据 以及输出数据

## 1.传统javaweb servlet的方式
HttpServletRequest 接口中的方法getParameter 通过采用javaweb的模式数据接收

## 2.根据属性名做接收数据（springmvc）
特点：前端参数名字需要和形参名字相同，否则不能接收



## 3.封装为javabean
此时，前端的字段需要和bean中的属性名相同，包括大小写，对于int类型的字段，若没有传递值过来，则为0，String的为null

如果是放在表单中输入的，输入控件的name属性需要和属性名相同

成员属性中如果没有get set方法也无法使用该方法，因为spring中是通过set方法来给bean对象封装值的

## 4.指定参数名
### 1.@reqeustParam 
指定客户端参数名称，与程序中实际参数名可以不同

### 2.@PathVariable 
参数直接给值的形式，用 / 分割

## 3.@RequestBody(推荐 前后端分离时)
@RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的)，所以只能发送POST请求。

> 使用前提：导入 Jackson 的 jar 包或 maven 坐标, 版本推荐2.9 以上。

# 程序设计=输入+算法+输出

## 比较常用的是restful api的方式处理输入输出
[https://zhuanlan.zhihu.com/p/617187396](https://zhuanlan.zhihu.com/p/617187396)

### 问题1 springmvc 前台传递string数据,后台参数为int,string转int400报错如何通过重写spring源码解决

### 问题2 参数检验问题
解决办法：[点击查看](https://blog.csdn.net/chongbeixie9244/article/details/101005408)


这个问题是由于在Spring框架中，前台向后台传递了字符串类型的数据，但是后台期望的是一个整数类型（int）的参数，因此在转换过程中出现了错误。
在Spring框架中，可以通过自定义一个参数解析器（ParameterHandler）来解决这个问题，将字符串类型的参数转换为整数类型。下面是一个示例代码：
java
复制

```
import org.springframework.core.MethodParameter;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import javax.servlet.http.HttpServletRequest;
public class CustomArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // 只处理int类型的参数
        return parameter.getParameterType().equals(int.class);
    }
    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, HttpServletRequest request, WebDataBinderFactory binderFactory) throws Exception {
        String value = request.getParameter(parameter.getParameterName());
        if (value == null || value.isEmpty()) {
            return null;
        }
        try {
            int intValue = Integer.parseInt(value);
            return intValue;
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Invalid int value: " + value, e);
        }
    }
}
```

上面的代码定义了一个CustomArgumentResolver类，它实现了HandlerMethodArgumentResolver接口，用于处理int类型的参数。supportsParameter方法用来判断是否支持处理该参数，而resolveArgument方法则是实际将字符串参数转换为整数的地方。
接下来，我们需要在Spring MVC的配置中注册这个参数解析器，让它能够被自动使用。可以在Spring配置中添加以下代码：
java
复制

```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import java.util.List;
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(new CustomArgumentResolver());
    }
}
```

这个WebConfig类实现了WebMvcConfigurer接口，并覆盖了addArgumentResolvers方法，将我们自定义的参数解析器添加到参数解析器的列表中。这样，当Spring MVC处理请求时，遇到int类型的参数，就会自动调用CustomArgumentResolver的resolveArgument方法，将字符串类型的参数转换为整数类型。如果转换失败，就会抛出一个IllegalArgumentException异常。

# 数据校验
方式：
##1.客户端校验 数据提交之前
使用js
## 2.服务端校验
无论收到啥样的数据提交给服务器，服务器接收到之后开始对数据格式开始比对，符合继续执行，反之停止
### 设计流程
控制器接收到数据后，比对（return boolean）

#### 1.方法一

不断使用if ,确实容易理解，但代码量多

#### 2.方法二(常用)

使用jsr303校验 要求数据要封装在java对象中，使用springmvc中接收类对象的方法

首先需要在javabean的属性上加注解

然后在springmvc中配置，在controller方法形参前面添加@Valid注解，形参最后，（注意顺序），创建一个BindingResult result的形参，这里的result用于存放校验结果

```
List<FieldError> fieldErrors = result.getFieldErrors();
            for (FieldError fieldError : fieldErrors) {
                System.out.println(fieldError.getField()+"=>>>"+fieldError.getDefaultMessage());
            }
```
getField获取属性名

getDefaultMessage 获取我属性上注解的messgae

### 使用jsr303校验中@notnull和@notempty的区别
@Null是说不能发改字段

@NotNull是指 前端不能不发改字段，不是值为不能为null

@notempty是说值不能为空

@Digits(integer=,fraction=) 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。

@Future 被注解的元素必须是日期，以后的日期，不能是现在之前的日期

@Past 必须是现在的以前的日期

### 正则表达式
![](https://img-blog.csdnimg.cn/cd5c0174b037464f91f37fd6f4b71014.png)

#### 前后加^$的作用

同时有^和$表示必须匹配整个字符串。

如：正则”^abc$”匹配字符串”abc”，但不能匹配”abcd”

@size 是指的字符串长度大小，不是数值大小

@range 数值大小

@Length 字符串长度大小

### postman的使用
传递bool值使用1 or 0 === true or false


