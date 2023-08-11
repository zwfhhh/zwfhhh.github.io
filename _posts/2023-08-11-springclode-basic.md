---
layout: post
title: SpringCloud基础知识
date: 2023-08-11 08:35:00 +0800
category: SpringCloud
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/microservice.jpg
icon: note
---


* content
{:toc}
# 关于对于springcloud中的注册中心和consume消费者和provier服务者之间的关系理解
## pringCloud provider（服务提供方） consumer（服务调用方） server（注册中心） 运行原理
# Provider
# 第一步 provider注册到server上
### provider 模块启动的时候 会通过发送rest请求的方式

### 把自己服务的一些元数据注册到eureka server注册中心

### EurekaServer接收到请求后 将数据存储在一个双层结构Map中

### 第一层的key是服务名，第二层的key是具体服务的实例名（一个服务有多个实例）

### 配置文件中 eureka.client.register-with-eureka=true

### 若为false 则不会启动注册操作

## 第二步 provider同步
### 两个provider分别注册到两个不同的注册中心上

### 两个注册中心相互注册为服务（高可用）

### 服务提供者发送注册请求到一个服务注册中心时 会转发到集群中的其他的注册中心 这样服务就能同步

### 两个provider的信息会被所有的注册中心同步

## 第三步 provider续约
### 当provider在server上注册成功的时候 provider会维持一个心跳（心跳机制）来通知server不要让注册中心将这个服务实例在服务列表中remove掉，这个保持心跳的操作就是 服务续约 （Renew）.服务续约的配置信息有两条比较重要
### eureka.instance.lease-renewal-interval-in-seconds = 30
### 表示eureka client间隔多久去拉取服务注册信息，默认为30秒，对于api-gateway，如果要迅速获取服务注册状态，可以缩小该值，比如5秒.
### eureka.instance.lease-expiration-duration-in-seconds = 90
### 表示eureka server至上一次收到client的心跳之后，等待下一次心跳的超时时间，在这个时间内若没收到下一次心跳，则将移除该instance。

## Consumer
## 第一步 获取服务
### 在注册中心里依旧注册了一个provider,并且有两个实例，启动consumer的时候，会发送一个rest请求给注册中心来获得注册中心上的服务清单 server 会维护一份onlyRead的清单返回给consumer,并且该缓存清单每隔30秒更新一次.获取服务的配置为 eureka.client.fetch-registry=true,如果是false就不能获取服务清单了，更新服务清单缓存时间的配置 eureka.client.registry-fetch-interval-seconds = 30 默认是三十秒

## 第二步 调用服务
### consumer在获取服务清单（所有provider）之后 通过provider的name拿到具体服务的实例名和该实例的元数据信息，在Ribbon中会采用轮询的方式去调用来获得负载均衡访问实例的时候 eureka有Region和Zone的概念 一个Region中里有很多Zone 每个Zone里都有一个服务客户端被注册.在进行服务调用的时候，优先会访问处于同一个Zone的服务提供方，如果调用不到才去别Zone访问。

## 第三步 服务下线
### consumer在正常关闭服务的时候 会触发一个consumer下线的请求给server,server接收到请求之后把这个服务状态设置为down,并且广播这个事件

## server
## 第一步 失效剔除
### 如果server没有接收到consumer或者provider下线的rest请求，server会自动创建一个定时任务，默认每隔60s剔掉当前清单中超时的（90s）的没有续约的服务请求

## 第二步 自我保护
### 本地启动server的时候，打开控制台，可以看到
### springCloud provider（服务提供方） consumer（服务调用方） server（注册中心） 运行原理
### 实际上是触发了server的自我保护 原因是因为provider注册到server上的时候会维护一个心跳连接，server在运行的时候会统计心跳失败的比例在15分钟是否低于85%.如果低于85%，server会把当前的实例注册信息保护起来，让这些实例不会过期。但是，如果在这段保护时间的时候实例若出现问题，那么consumer很容易拿到实际不存在的实例，会出现调用失败的情况，所以provider必须有容错机制，比如可以请求重试，断路器等机制。
### 由于本地调试很容易出现server保护机制，会让server维护的实例不那么准确，所以本地开发的时候可以使用eureka.server.enable-self-preservation=false来关闭保护机制，已确保剔除失效的实例。