---
title: 同步阻塞式IO
date: 2017-10-02 23:56:33
tags: IO
categories: Netty
---
### BIO通信模型
通常由一个独立到Acceptor线程负责监听客户端连接，他接收到客户端连接请求后，为每个客户端创建一个新的线程进行链路处理。处理完后通过输出流返回给客户端，线程销毁。这是典型到一请求一应答通信模型。
![](https://ww1.sinaimg.cn/large/005Y4715gy1fjk0aouwa0j30nh07yadu.jpg)

#### 服务端TimeServer

```
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * Created by hadoop on 2017/9/14.
 */
public class TimeServer {
    public static void main(String[] args) {
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
            //创建一个服务端socket
            server = new ServerSocket(port);
            System.out.println("the time server is start in port:"+port);
            Socket socket =    null;
            //利用循环来监听客户端到连接，
            while(true){
                //如果没有连接接入，则阻塞在这一步
                socket = server.accept();
                //如果有连接接入
                new Thread(new TimeServerHandler(socket)).start();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (server != null){
                System.out.println("The time server close");
                try {
                    server.close();
                }catch (IOException e){
                    e.printStackTrace();
                }
                server=null;
            }
        }

    }
}
```
![](https://ww1.sinaimg.cn/large/005Y4715gy1fjk3muq2nkj30gl078mxc.jpg)

上图是java visual vm 的service线程的堆栈信息的一部分。如图：服务端启动后，会阻塞在循环的31行的accept方法。如果有客户端发送连接过来，跳出accept，执行下面的命令，产生一个新的线程跟客户端通信。

#### 服务端处理方法 TimeServerHandler

```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.util.Date;

/**
 * Created by hadoop on 2017/9/14.
 */
public class TimeServerHandler implements Runnable {
    private Socket socket;
    //获取服务端的ServerSocket
    public TimeServerHandler(Socket socket){
        this.socket = socket;
    }
    @Override
    public void run() {
        //定义IO流读方法
        BufferedReader in = null;
        //定义IO流写方法
        PrintWriter  out = null;
        try {
            //读取socket的输入流
            in = new BufferedReader(new InputStreamReader(this.socket.getInputStream()));
            //写到socket到输出流
            out = new PrintWriter(this.socket.getOutputStream(),true);
            String currentTime = null;
            String body = null;
            while (true){
                body = in.readLine();
                if (body ==null){
                    break;
                }
                System.out.println("The time server receive order");
                //如果从socket获取到数据是QUERY TIME ORDER，将当前到系统时间发送给客户端，否则将BAD ORDER发送给客户端
                currentTime = "QUERY TIME ORDER".equalsIgnoreCase(body)? new Date(System.currentTimeMillis()).toString():"BAD ORDER";
                out.println(currentTime);
            }
        }catch (Exception e){
            if (in != null){
                try {
                    //关闭输入流
                    in.close();
                }catch (Exception el){
                    el.printStackTrace();
                }
            }
            if (out!= null){
                //关闭输出流
                out.close();
                out=null;
            }
            if (this.socket!=null){
                try{
                    //关闭socket
                    this.socket.close();
                }catch (IOException e1){
                    e1.printStackTrace();
                }
                this.socket = null;
            }

        }
    }
}
```
#### BIO客户端

```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;

/**
 * Created by hadoop on 2017/9/14.
 */
public class TimeClient {
    public static void main(String[] args) {
        //定义要传输信息的服务端的端口号
        int port = 8080;
        if (args != null && args.length>0){
            try {
                port = Integer.valueOf(args[0]);
            }catch (NumberFormatException e){
                e.printStackTrace();
            }
        }
        Socket socket = null;
        BufferedReader in = null;
        PrintWriter out = null;
        try{
            //新建socket连接
            socket = new Socket("127.0.0.1",port);
            //从socket通道读取数据
            in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            //写取数据到socket通道
            out = new PrintWriter(socket.getOutputStream(),true);
            //先向socket发送数据，然后从socket读取数据，再打印出来
            out.println("QUERY TIME ORDER");
            System.out.println("send order 2 server succeed");
            String resp = in.readLine();
            System.out.println("now is :"+resp);
        }catch (Exception e){

        }finally {
            //关闭输出流
            if (out!=null){
                out.close();
                out= null;
            }
            //关闭输入流
            if (in != null){
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                in=null;
            }
            //关闭socket通道
            if (socket != null){
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                socket = null;
            }
        }

    }
}

```
