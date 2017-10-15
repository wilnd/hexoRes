---
title: Http协议
date: 2017-10-15 21:20:54
tags: 网络与安全
categories: 网络与安全
---
### 概述
HTTP协议的主要特点可概括如下：
1. 支持客户/服务器模式
2. 简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
3. 灵活：HTTP允许<font color="#FF0000">传输任意类型的数据对象</font>。正在传输的类型由Content-Type加以标记
4. 无连接：无连接的含义是限制HTTP允许<font color="#FF0000">每次连接只处理一个请求</font>。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间
5. HTTP协议是无状态协议。无状态是指协议无连接的含义是限制HTTP允许<font color="#FF0000">对于事务处理没有记忆能力</font>。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快

### HTTP URL
host表示合法的Internet主机域名或者IP地址；port指定一个端口号，为空则使用缺省端口80；abs_path指定请求资源的URI；如果URL中没有给出abs_path，那么当它作为请求URI时，必须以“/”的形式给出

```
http://host[":"port][abs_path]
```

### HTTP之请求组成 ：请求行，消息报头，响应正文
#### 请求：
请求行以一个方法符号开头，以空格分开，后面跟着请求的URI和协议的版本，格式如下

```
请求：Method Request-URI HTTP-Version CRLF
```
其中 Method表示请求方法；Request-URI是一个统一资源标识符；HTTP-Version表示请求的HTTP协议版本；CRLF表示回车和换行（除了作为结尾的CRLF外，不允许出现单独的CR或LF字符）。

根据HTTP标准，HTTP请求可以使用多种请求方法
1. HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法。
2. HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。

![](http://ww1.sinaimg.cn/large/005Y4715gy1fkj98pah31j30j509dwg6.jpg)

##### 注意：
GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。 GET和POST还有一个重大区别，简单的说：GET产生一个TCP数据包；POST产生两个TCP数据包。对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）； 而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

![](http://ww1.sinaimg.cn/large/005Y4715gy1fkjcc6t4k9j30mg0d20tp.jpg)

#### 2. 响应
HTTP响应由三个部分组成：状态行、消息报头、响应正文
1. 状态行

```
响音：HTTP-Version Status-Code Reason-Phrase CRLF

```
1. HTTP-Version表示服务器HTTP协议的版本；
2. Status-Code表示服务器发回的响应状态代码；
3. Reason-Phrase表示状态代码的文本描述

状态代码有三位数字组成，第一个数字定义了响应的类别，且有五种可能取值：

```
1xx：指示信息--表示请求已接收，继续处理
2xx：成功--表示请求已被成功接收、理解、接受
3xx：重定向--要完成请求必须进行更进一步的操作
4xx：客户端错误--请求有语法错误或请求无法实现
5xx：服务器端错误--服务器未能实现合法的请求
```
常见状态：

```
200 OK  //客户端请求成功
400 Bad Request  //客户端请求有语法错误，不能被服务器所理解
401 Unauthorized //请求未经授权，这个状态代码必须和WWW-Authenticate报 //头域一起使用
403 Forbidden  //服务器收到请求，但是拒绝提供服务
404 Not Found  //请求资源不存在，eg：输入了错误的URL
500 Internal Server Error //服务器发生不可预期的错误
503 Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后,可能恢复正常
```



#### 2. 消息报头
#### 3. 请求正文