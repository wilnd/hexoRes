---
title: netty-构成
date: 2017-09-05 23:54:32
tags: javaWeb
categories: javaWeb
---

###### 非阻塞 I/O 不会强迫我们等待操作的完成。在这种能力的基础上，真正的异步 I/O 起到了更进一步的作用<font color="#FF0000">一个异步方法完成时立即返回并直接或稍后通知用户</font>。
>Channel

##### Channel 是 NIO <font color="#FF0000">基本的结构</font>。它代表了一个用于连接到实体如硬件设备、文件、网络套接字或程序组件,能够执行一个或多个不同的 I/O 操作（例如读或写）的开放连接。

>Callback

##### callback (回调)是一个简单的方法,提供给另一种方法作为引用,这样<font color="#FF0000">后者就可以在某个合适的时间调用前者</font>。这种技术被广泛使用在各种编程的情况下,最常见的方法之一通知给其他人操作已完成。+

##### Netty 内部使用回调处理事件时。一旦这样的回调被触发，事件可以由接口 ChannelHandler 的实现来处理。如下面的代码

```
public class ConnectHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {   //1
        System.out.println(
                "Client " + ctx.channel().remoteAddress() + " connected");
    }
}
```
1. 当建立一个新的连接时调用 ChannelActive()

> Future

##### Future 提供了另外一种<font color="#FF0000">通知应用操作已经完成的方式</font>。这个对象作为一个异步操作结果的占位符,它将在将来的某个时候完成并提供结果。

##### ChannelFuture 提供多个附件方法来允许一个或者多个 ChannelFutureListener 实例.个回调方法 operationComplete() 会在操作完成时调用。<font color="#FF0000">事件监听者能够确认这个操作是否成功或者是错误</font>。ChannelFutureListener 提供的通知机制不需要手动检查操作是否完成的。

##### 每个 Netty 的 outbound I/O 操作都会返回一个 ChannelFuture;这样就不会阻塞。这就是 Netty 所谓的<font color="#FF0000">自底向上的异步和事件驱动</font>。

##### 下面代码描述了如何利用 ChannelFutureListener 。首先，连接到远程地址。接着，通过 ChannelFuture 调用 connect() 来 注册一个新ChannelFutureListener。当监听器被通知连接完成，我们检查状态。如果是成功，就写数据到 Channel，否则我们检索 ChannelFuture 中的Throwable。

```
Channel channel = ...;
//不会阻塞
ChannelFuture future = channel.connect(            //1
        new InetSocketAddress("192.168.0.1", 25));
future.addListener(new ChannelFutureListener() {  //2
@Override
public void operationComplete(ChannelFuture future) {
    if (future.isSuccess()) {                    //3
        ByteBuf buffer = Unpooled.copiedBuffer(
                "Hello", Charset.defaultCharset()); //4
        ChannelFuture wf = future.channel().writeAndFlush(buffer);                //5
        // ...
    } else {
        Throwable cause = future.cause();        //6
        cause.printStackTrace();
    }
}
});
```
1. 异步连接到远程对等节点。调用立即返回并提供 ChannelFuture。
2. 操作完成后通知注册一个 ChannelFutureListener 。
3. 当 operationComplete() 调用时检查操作的状态。
4. 如果成功就创建一个 ByteBuf 来保存数据。
5. 异步发送数据到远程。再次返回ChannelFuture。
6. 如果有一个错误则抛出 Throwable,描述错误原因。
> Event 和 Handler
##### Netty 使用不同的事件来通知我们更改的状态或<font color="#FF0000">操作的状态</font>.这使我们能够根据发生的事件触发适当的行为:
- 日志
- 数据转换
- 流控制
- 应用程序逻辑
##### Netty 是一个网络框架,事件很清晰的跟<font color="#FF0000">入站或出站数据流</font>相关。因为一些事件可能触发<font color="#FF0000">传入的数据</font>或<font color="#FF0000">状态的变化</font>:
- 活动或非活动连接
- 数据的读取
- 用户事件
- 错误
出站事件是由于在<font color="#FF0000">未来操来将触发</font>一个动作：
- 打开或关闭一个连接到远程
- 写或冲刷数据到 socket
![](https://ww1.sinaimg.cn/large/005Y4715gy1fj948oocb5j30k507gjtl.jpg)
##### Netty 的 ChannelHandler 是各种<font color="#FF0000">处理程序的基本抽象</font>。想象下，每个处理器实例就是一个回调，用于执行对各种事件的响应。
>整合

1. FUTURE, CALLBACK 和 HANDLER
##### Netty 的异步编程模型是建立在 future 和 callback 的概念上的。

##### 拦截操作和转换入站或出站数据只需要您提供回调或利用 future 操作返回的。这使得链操作简单、高效,促进编写可重用的、通用的代码。一个 Netty 的设计的主要目标是促进<font color="#FF0000">关注点分离</font>:你的业务逻辑从网络基础设施应用程序中分离。
2. SELECTOR, EVENT 和 EVENT LOOP
##### Netty 通过触发事件从应用程序中抽象出 Selector，从而避免手写调度代码。EventLoop 分配给每个 Channel 来处理所有的事件，包括
- 注册感兴趣的事件
- 调度事件到 ChannelHandler
- 安排进一步行动
##### EventLoop 本身是由只有一个线程驱动，它给一个 Channel 处理所有的 I/O 事件，并且在 EventLoop 的生命周期内不会改变。
