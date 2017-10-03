---
title: netty-为什么需要他
date: 2017-09-05 23:54:07
tags: javaWeb
categories: javaWeb
---
>为什么用netty？

###### 我们总会有这样的需求，以较低的成本交付来换取更大的<font color="#FF0000">吞吐量</font>和<font color="#FF0000">可用性</font>。可用性这一点是非常重要的。我们从漫长的痛苦的经验学习到，低级别的 API 不仅暴露了高级别直接使用的复杂性，而且引入了过分依赖于这项技术所造成的短板。因此，面向对象的一个基本原则：<font color="#FF0000">通过抽象来隐藏背后的复杂性</font>。
目前netty实现了的协议就有 FTP, SMTP, HTTP, WebSocket 和 SPDY 以及其他二进制和基于文本的协议。

下面展示了 Netty 技术和方法的特
- 设计
 - 针对多种传输类型的统一接口 - 阻塞和非阻塞
 - 简单但更强大的线程模型
 - 真正的无连接的数据报套接字支持
 - 链接逻辑支持复用
- 易用性
 - 大量的 Javadoc 和 代码实例
 - 除了在 JDK 1.6 + 额外的限制。（一些特征是只支持在Java 1.7 +。可选的功能可能有额外的限制。）
- 性能
 - 比核心 Java API 更好的吞吐量，较低的延时
 - 资源消耗更少，这个得益于共享池和重用
 - 减少内存拷贝
- 健壮性
 - 消除由于慢，快，或重载连接产生的 OutOfMemoryError
 - 消除经常发现在 NIO 在高速网络中的应用中的不公平的读/写比
- 安全
 - 完整的 SSL / TLS 和 StartTLS 的支持
 - 运行在受限的环境例如 Applet 或 OSGI
- 社区
 - 发布的更早和更频繁
 - 社区驱动
- 异步和事件驱动
 - 异步，即非同步事件，当然是跟你日常生活的类似。例如，您可以发送电子邮件；可能得到或者得不到任何回应，或者当你发送一个您可能会收到一个消息。
>#### Netty 资料[Netty start](//waylau.gitbooks.io/netty-4-user-guide/Preface/The%20Solution.html)   [Netty 实战(精髓)](https://waylau.gitbooks.io/essential-netty-in-action/GETTING%20STARTED/Introducing%20Netty.html)

>#### BIO 阻塞式IO

![](http://ww1.sinaimg.cn/large/005Y4715gy1fj92asb8scj30fx0acta4.jpg)

```
ServerSocket serverSocket = new ServerSocket(portNumber);//1
Socket clientSocket = serverSocket.accept();             //2
BufferedReader in = new BufferedReader(                     //3
        new InputStreamReader(clientSocket.getInputStream()));
PrintWriter out =
        new PrintWriter(clientSocket.getOutputStream(), true);
String request, response;
while ((request = in.readLine()) != null) {                 //4
    if ("Done".equals(request)) {                         //5
        break;
    }
}
response = processRequest(request);                        //6
out.println(response);                                    //7
}            
```
1. ServerSocket 创建并监听端口的连接请求
2. accept() 调用阻塞，直到一个连接被建立了。返回一个新的 Socket 用来处理 客户端和服务端的交互
3. 流被创建用于处理 socket 的输入和输出数据。BufferedReader 读取从字符输入流里面的本文。PrintWriter 打印格式化展示的对象读到本文输出流
4. 处理循环开始 readLine() 阻塞，读取字符串直到最后是换行或者输入终止。
5. 如果客户端发送的是“Done”处理循环退出
6. 执行方法处理请求，返回服务器的响应
7. 响应发回客户端
8. 处理循环继续
##### 点评：这段代码限制每次只能处理一个连接。为了实现多个并行的客户端我们需要分配一个新的 Thread 给每个新的客户端 Socket(当然需要更多的代码)。但考虑使用这种方法来支持大量的同步，长连接。在任何时间点多线程可能处于休眠状态，等待输入或输出数据。这很容易使得资源的大量浪费，对性能产生负面影响。
>NIO 非阻塞式IO

#### SELECTOR是 Java 的无阻塞 I/O 实现的关键
![](http://ww1.sinaimg.cn/large/005Y4715gy1fj92cpeip8j30fw0dj401.jpg)
#### 分析：Selector 最终决定哪一组注册的 socket 准备执行 I/O。通过通知，一个线程可以同时处理多个并发连接。总体而言，该模型提供了比 阻塞 I/O 模型 更好的资源使用，因为
- 可以用较少的线程处理更多连接，这意味着更少的开销在内存和上下文切换上
- 当没有 I/O 处理时，线程可以被重定向到其他任务上。
#### 实现可靠和可扩展的 event-processing（事件处理器）来处理和调度数据并保证尽可能有效地，这是一个繁琐和容易出错的任务，最好留给专家 <font color="#FF0000">Netty</font> 。