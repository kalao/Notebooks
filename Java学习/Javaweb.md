

# 1 . Java web

## 1.1 信息,服务,应用程序

web保存着信息,这些信息都保存在世界的某一台主机上.

信息是一种服务,信息的提供就是服务的提供,因此这些提供服务的主机又称为服务器.

那将信息分类,就相当于服务分类,这些服务对应某个应用程序.从而服务器就是管理这些应用程序的主机.



一个应用程序又包括静态资源(HTML CSS JS)和动态资源(jsp servlet),而javaweb就是开发这样的应用程序.

静态资源:HTML+CSS

动态资源:网页数据可以动态变化,不同时间,不同的人看到的都`可以不一样.



## 1.2 客户端和服务端,访问资源过程

客户端是服务的请求者,服务端顾名思义是服务的提供者.

客户端可以通过资源路径可以访问服务端的静态资源

而对应于动态资源,我们需要一个将数据整合至静态资源的过程,这是一个动态生成的过程.

![image-20210124213111553](/home/kalao/.config/Typora/typora-user-images/image-20210124213111553.png)

这个过程有些缺点,比如出现问题,就需要停机维护;但是优点是可以动态生成页面.

> 如何访问一个网站?

1. 首先获得域名

2. 去查看hosts配置文件,有没有对应的域名映射,如果有就找到对应IP,如果没有就去DNS服务器找

   

## 1.3 web 服务器

IIS: 微软的 

Tomcat:开源,轻量, Tomcat实际上运行JSP和Servlet

### 1.3.1 Tomcat

1. conf/server.xml

   1. 配置端口 Connector标签下port属性,注意默认8080,https默认443
   2. 配置主机名 Host标签下的name名称

2. 发布一个服务

   1. 放在Tomcat指定的web应用的文件夹(webapps)下

      > --webapps
      >
      > ​	-kalao 网站的目录名
      >
      > ​           -WEB-INF
      >
      > ​                 -classes :java程序
      >
      > ​				-lib:j依赖
      >
      > ​                -web.xml 网站配置
      >
      >  		 -index.html 默认主页      
      >
      > ​          -static 
      >
      > ​				-css
      >
      > ​				-js
      >
      > ​				-img             





## 1.4协议

### 1.4.1 HTTP

超文本传输协议,基于TCP的简单请求-响应协议.

网站的资源访问可以基于HTTP

- http1.0 和http2.0 区别

> 连接之后http1.0只能获得一个资源,而http2.0可以获得多个.

### 1.4.2 HTTPS

1. 安全的

### 1.4.3 请求

> 请求分为请求头和消息,消息分为消息头和消息

![image-20210124230921406](/home/kalao/.config/Typora/typora-user-images/image-20210124230921406.png)

#### 1.4.3.1 请求行

![image-20210124231257017](/home/kalao/.config/Typora/typora-user-images/image-20210124231257017.png)

#### 1.4.3.2 消息头

![image-20210124231559773](/home/kalao/.config/Typora/typora-user-images/image-20210124231559773.png)

### 1.4.4 响应 

![image-20210124230800748](/home/kalao/.config/Typora/typora-user-images/image-20210124230800748.png)

#### 1.4.4.1 响应状态码

| 200  | 请求OK                 |      |
| ---- | ---------------------- | ---- |
| 3xx  | 请求重定向             |      |
| 4xx  | 找不到资源             |      |
| 5xx  | 服务器错误 502网关错误 |      |





#### 1.4.4.2 响应体

![image-20210124231948914](/home/kalao/.config/Typora/typora-user-images/image-20210124231948914.png)





## 1.5 相关的技术

PHP

- PHP开发速度快,功能很强大,跨平台
- 无法承载大访问量

JSP/Servlet

- 基于B/S,基于Java语言
- 可以承载三高问题(高并发,高性能,高可用)

maven 

- 管理依赖
- 约定大于配置

![image-20210125093240106](/home/kalao/.config/Typora/typora-user-images/image-20210125093240106.png)

![image-20210125094100024](/home/kalao/.config/Typora/typora-user-images/image-20210125094100024.png)

![image-20210125094627244](/home/kalao/.config/Typora/typora-user-images/image-20210125094627244.png)

![image-20210125094907956](/home/kalao/.config/Typora/typora-user-images/image-20210125094907956.png)

全局配置

![image-20210125100736122](/home/kalao/.config/Typora/typora-user-images/image-20210125100736122.png)



## 1.6 Servlet

## ![image-20210125102104908](/home/kalao/.config/Typora/typora-user-images/image-20210125102104908.png)

![image-20210125110643665](/home/kalao/.config/Typora/typora-user-images/image-20210125110643665.png)



