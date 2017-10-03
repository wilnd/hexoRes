---
title: 伪异步IO
date: 2017-10-02 23:55:08
tags: 通信
categories: Netty
---
### BIO改进
同步阻塞IO面临一个链路需要一个线程处理到问题。后来有人对他到线程进行优化：后端使用一个线程池来处理多个客户端的请求接入，形成客户端个数M 线程池最大线程数N的比例关系，其中M可以远远大于N。通过线程池可以灵活到调配线程资源，减少了创建与销毁线程到开销。设置线程到最大值，防止由于海量并发接入导致到线程耗尽。
![](http://ww1.sinaimg.cn/large/005Y4715gy1fjk7pn65g3j30p007n0xd.jpg)
如图：伪异步IO跟同步阻塞式IO的差别是多了一个环节--线程池，客户端是没有变化的。

### TimeServer 服务端

```
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Created by hadoop on 2017/9/15.
 */
public class TimeServer {
    public static void main(String[] args) throws IOException {
        //定义服务端到默认端口号
        int port = 8080;
        //如果传了参数进来，端口号为外在参数
        if (args != null && args.length>0){
            try {
                port = Integer.valueOf(args[0]);
            }catch (NumberFormatException e){
                e.printStackTrace();
            }
        }
        ServerSocket server = null;
        try {
            //新建一个服务端socket
            server = new ServerSocket(port);
            System.out.println("The time server is  start in port:"+port);
            Socket socket = null;
            //新建一个线程池
            TimeServerHandlerExecutePool  singleExecutor = new TimeServerHandlerExecutePool(50,1000);
            while (true) {
                //如果没有请求过来阻塞在accept
                socket = server.accept();
                //如果有请求过来交由线程池来管理
                singleExecutor.execute(new TimeServerHandler(socket));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (server!= null) {
                System.out.println("The time server close");
                server.close();
                server = null;
            }
        }
    }
}
```
### 线程池定义

```
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * Created by hadoop on 2017/9/15.
 */
public class TimeServerHandlerExecutePool {
    //声明ExecutorService线程池
    private ExecutorService executor;
    //构造方法给线程池赋值
    public TimeServerHandlerExecutePool(int maxPoolSize , int queueSize) {
        executor = new ThreadPoolExecutor(Runtime.getRuntime().availableProcessors(),
                maxPoolSize,
                120L,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<Runnable>(queueSize));
    }
    //线程执行方法
    public void execute(java.lang.Runnable task) {
        executor.execute(task);
    }
}
```

### 线程池类图：

![](http://ww1.sinaimg.cn/large/005Y4715gy1fjk8ych9cjj306b096q31.jpg)

