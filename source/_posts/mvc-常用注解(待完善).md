---
title: mvc注解
date: 2018-04-01 15:17:17
tags: java spring
categories: java
---

1. <context:component-scan base-package="com.yhglobal.gongxiao"/>

```
用来扫描包下的注解
@Autowired 注解告诉spring，这个字段是需要自动注入的。
service上的注解
@Service 告诉spring容器，这是一个Service类，默认情况会自动加载它到spring容器里。
dao上的注解
@Scope 指定此spring bean的scope是单例
@Repository 指定此类是一个容器类

```
2. <context:component-scan base-package="com.xx.xx" />

```
扫描定时任务所在的目录
```
3. <context:property-placeholder location="classpath:yhportalconfig_${server.mode}.properties"/>

```
加载property文件的内容
```
4. <mvc:annotation-driven>

```
<mvc:annotation-driven>会自动注册RequestMappingHandlerMapping与RequestMappingHandlerAdapter两个Bean,
这是Spring MVC为@Controller分发请求所必需的，并且提供了数据绑定支持，
@NumberFormatannotation支持，
@DateTimeFormat支持,
@Valid支持读写XML的支持（JAXB）和
读写JSON的支持（默认Jackson）等功能。
```
5. <mvc:resources mapping="/api/**" location="/api/"/>

```
<mvc:default-servlet-handler />

DispatcherServlet不会拦截以/api开头的所有请求路径，并当作静态资源交由Servlet处理。
```
6. org.springframework.web.servlet.view.InternalResourceViewResolver

```
把DispatcherServlet的view解析为jsp
```
7. mvc:interceptors

```
<mvc:mapping path="/**"/> 拦截器路径配置
<mvc:exclude-mapping path="/css/**"/>
```
8. <bean class="com.yhglobal.gongxiao.base.model.PortalUserInfo" scope="request">

```
1.singleton Spring IOC容器中只会存在一个共享的bean实例
2.prototype 每一次请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）都会产生一个新的bean实例 
是Spring不能对一个prototype bean的整个生命周期负责
3.request 每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效
4.session 每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效
5.global session
6.自定义
```
9. <aop:scoped-proxy />




