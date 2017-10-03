---
title: NIO
date: 2017-10-02 23:57:43
tags: 通信
categories: Netty
---
##### NIO编程比BIO编程难度大很多，如果加上半包读，半包写代码会更复杂，为什么这么复杂NIO到应用越来越广泛了？
- 客户端发送的请求是异步到，可以通过在多路复用器注册op_connect等待后续结果，不需要像之前客户端那样被同步阻塞
- SocketChannel操作都是异步的，如果没有可读写到操作他不会等待，而是返回。这样IO线程可以处理其他链路。
- 线程模型优化，一个多路选择器线程可以同时处理成千上万个客户端连接，而且性能不会随着客户端连接数到增加而直线下降
#### SocketChannel
![](http://ww1.sinaimg.cn/large/005Y4715gy1fjl7r8lbx7j30os0icq3n.jpg)
#### ServerSocketChannel
![](http://ww1.sinaimg.cn/large/005Y4715gy1fjl7pyzix5j30c90i2t98.jpg)

### TimeServer
#### 服务端序列图
![](http://ww1.sinaimg.cn/large/005Y4715gy1fjlev8ff3jj30m90dkq5f.jpg)

```
/**
 * Created by hadoop on 2017/9/15.
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
        //创建一个多路复用器，它是一个独立到线程，负责轮询多路复用器Selector，可以处理多个客户端到并发接入
        MultiplexerTimeServer timeServer = new MultiplexerTimeServer(port);
        new Thread (timeServer,"NIO-MultiplexerTimeServer-001").start();
    }
}
```

### MultiplexerTimeServer
```
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Date;
import java.util.Iterator;
import java.util.Set;

/**
 * Created by hadoop on 2017/9/15.
 */
public class MultiplexerTimeServer implements Runnable {
    //多路复用器
    private Selector selector;

    private ServerSocketChannel serverChannel;

    private volatile boolean stop;

    /**
     * 初始化多路复用器，绑定监听端口
     * @param port 端口号
     */
    public  MultiplexerTimeServer(int port){
        try {
            //设置多路复用器
            selector = Selector.open();
            //设置服务端通信通道，监听流式socket
            serverChannel = ServerSocketChannel.open();
            //设置服务端通信通道为非阻塞模式
            serverChannel.configureBlocking(false);
            //设置TCP的相关参数：地址，backlog
            serverChannel.socket().bind(new InetSocketAddress(port),1024);
            //将服务端通信通道注册到多路复用器上，监听SelectionKey.OP_ACCEPT操作位
            serverChannel.register(selector, SelectionKey.OP_ACCEPT);
            System.out.println("The time server is start int port :"+port);
        } catch (IOException e) {
            e.printStackTrace();
            System.exit(1);
        }

    }

    public void stop(){
        this.stop = true;
    }

    @Override
    public void run() {
        while(!stop){
            try {
                //每隔一秒多路复用器轮询一次准备就绪的channel
                selector.select(1000);
                //当有处于就绪状态的channel时，selector将返回channel的SelectionKey集合。
                Set<SelectionKey> selectedKeys = selector.selectedKeys();
                //遍历SelectionKey集合，即遍历当前有效的通道
                Iterator<SelectionKey> it = selectedKeys.iterator();
                SelectionKey key = null;
                while (it.hasNext()){
                    //获取当前所遍历的key，赋值给新的饮用
                    key = it.next();
                    //将当前遍历的key从集合里面移除
                    it.remove();
                    try {
                        //处理当前的通道
                        handleInput(key);
                    }catch (Exception e){
                        if (key != null){
                            key.cancel();
                            if (key.channel() != null){
                                key.channel().close();
                            }
                        }
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (selector!=null){
            try {
                selector.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 处理新接入客户端请求消息，通过selectionKey判断网络事件类型
     * @param key 就绪状态的通道
     * @throws IOException
     */
    private void handleInput(SelectionKey key) throws IOException {
        //如果当前通道是有效的
        if (key.isValid()){
            //下述操作相当于完成了TCP三次握手，TCP物理链路正式成立
            //如果当前key的通道已经准备好接收一个新的socket连接
            if (key.isAcceptable()){
                ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
                SocketChannel sc = ssc.accept();
                //设置SocketChannel为异步非阻塞模式
                sc.configureBlocking(false);
                sc.register(selector,SelectionKey.OP_READ);
            }
            //如果当前key的通道准备好读取数据
            if (key.isReadable()){
                SocketChannel sc = (SocketChannel) key.channel();
                //创建一个ByteBuffer,无法知道客户端发送的码流大小，开辟一个1MB到缓冲区
                ByteBuffer readBuffer = ByteBuffer.allocate(1024);
                //使用socketChannel到read方法读取请求码流
                int readBytes = sc.read(readBuffer);
                //返回值大于0表示读取到了字节，对字节进行编解码
                if (readBytes>0){
                    //limit=position , position=0,重置mask。一般是结束buf操作，将buf写入输出流时调用，这个必须要调用，否则极有可能position!=limit，导致position后面没有数据，每次写入数据到输出流时，必须确保position=limit。
                    readBuffer.flip();
                    //返回缓冲区剩余容量，取值=容量-位置
                    byte [] bytes = new byte[readBuffer.remaining()];
                    readBuffer.get(bytes);
                    String body = new String (bytes,"UTF-8");
                    System.out.println("The time server receive order:"+body);
                    String currentTime = "QUERY TIME ORDER".equalsIgnoreCase(body)?new Date(System.currentTimeMillis()).toString():"BAD QUERY";
                    doWrite(sc,currentTime);
                }else if (readBytes<0){
                    //返回值为-1表示链路已经关闭，需要关闭SocketChannel，释放资源
                    key.cancel();
                    sc.close();
                }else {
                    //=0表示没有读取到字节
                }
            }
        }
    }

    /**
     * 将应答消息异步发送给客户端
     * @param channel 将要写入数据的通道
     * @param response 写入数据到内容
     * @throws IOException
     */
    private void doWrite(SocketChannel channel,String response) throws IOException {
        if (response!=null && response.trim().length()>0){
            //将字符串编码成字节数组
            byte[] bytes = response.getBytes();
            //根据字节数组到容量创建ByteBuffer
            ByteBuffer  writeBuffer  = ByteBuffer.allocate(bytes.length);
            //将自己数组复制到缓冲区
            writeBuffer.put(bytes);
            //对缓冲区进行flip操作
            writeBuffer.flip();
            //将缓冲区到字节数组发送出去
            channel.write(writeBuffer);
        }
    }

}

```
### Timeclient
#### 客户端序列图
![](http://ww1.sinaimg.cn/large/005Y4715gy1fjlew2dxbkj30l40gldj1.jpg)
```
/**
 * Created by hadoop on 2017/9/15.
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
        //新建一个客户端线程发起连接
        new Thread(new TimeClientHandle("127.0.0.1",port),"TimeClient-0001").start();
    }
}
```

### TimeClientHandler


```
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

/**
 * Created by hadoop on 2017/9/15.
 */
public class TimeClientHandle implements Runnable {

    private String host;

    private int port;

    private Selector selector;

    private SocketChannel socketChannel;

    private volatile boolean stop;

    /**
     *初始化客户端参数，
     * @param host 地址
     * @param port 端口号
     */
    public TimeClientHandle(String host,int port){
        this.host = host ==null?"127.0.0.1":host;
        this.port = port;
        try {
            selector = Selector.open();
            socketChannel = SocketChannel.open();
            socketChannel.configureBlocking(false);
        } catch (IOException e) {
            e.printStackTrace();
            System.exit(1);
        }

    }

    @Override
    public void run() {
        try {
            //连接
            doConnect();
        } catch (IOException e) {
            e.printStackTrace();
            System.exit(1);
        }
        while (!stop) {
            try {
                //每秒轮询遍历一次所有通道
                selector.select(1000);
                //获取所有通道到key
                Set<SelectionKey> selectionKeys = selector.selectedKeys();
                //遍历所有通道
                Iterator<SelectionKey> it = selectionKeys.iterator();
                SelectionKey key = null;
                while (it.hasNext()){
                    key = it.next();
                    //将已遍历到通道从通道集合中移除
                    it.remove();
                    try {
                        //处理当前通道事件
                        handelInput(key);
                    }catch (Exception e){
                        //如果发生异常，将当前通道关闭
                        if (key!= null){
                            key.cancel();
                            if (key.channel()!= null){
                                key.channel().close();
                            }
                        }
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        //读完数据将多路复用选择器关闭
        if (selector!=null){
            try {
                selector.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 处理当前通道
     * @param key 通道的key
     * * @throws IOException
     */
    private void handelInput(SelectionKey key) throws IOException {
        //如果当前通道是有效的
        if (key.isValid()){
            SocketChannel sc = (SocketChannel) key.channel();
            //如果当前通道没有完成，或者没有
            if (key.isConnectable()) {
                if (sc.finishConnect()){
                    //注册为读状态
                    sc.register(selector,SelectionKey.OP_READ);
                    //通过通道发送数据
                    doWrite(sc);
                }else {
                    System.exit(1);
                }
            }
            //如果当前通道是可读到
            if (key.isReadable()){
                ByteBuffer readBuffer = ByteBuffer.allocate(1024);
                int readBytes = sc.read(readBuffer);
                if (readBytes>0){
                    readBuffer.flip();
                    byte[] bytes = new byte[readBuffer.remaining()];
                    readBuffer.get(bytes);
                    String body = new String(bytes,"UTF-8");
                    System.out.println("Now is："+  body);
                    this.stop = true;
                }else if (readBytes<0){
                    //对端链路关闭
                    key.cancel();
                    sc.close();
                }else {
                    //读到0字节 忽略
                }
            }
        }
    }

    /**
     * 如果通信通道没有建立，则将通道绑定在多路复用器上；
     * 如果通信通道已建立，将当前通道状态置为读
     * @throws IOException
     */
    private void doConnect() throws IOException {
        //如果通信通道已建立
        if (socketChannel.connect(new InetSocketAddress(host,port))){
            //注册到多路复用器上，状态为读。
            socketChannel.register(selector,SelectionKey.OP_READ);
            //发送请求数据
            doWrite(socketChannel);
        }else {
            //如果通信通道没有建立，则向多路复用器发起连接
            socketChannel.register(selector,SelectionKey.OP_CONNECT);
        }
    }

    /**
     *
     * @param socketChannel socket通道
     * @throws IOException
     */
    private void doWrite(SocketChannel socketChannel) throws IOException {
        byte[] req = "QUERY TIME ORDER".getBytes();
        ByteBuffer writeBuffer = ByteBuffer.allocate(req.length);
        writeBuffer.put(req);
        writeBuffer.flip();
        socketChannel.write(writeBuffer);
        if (!writeBuffer.hasRemaining()){
            System.out.println("send order 2 server succeed");
        }
    }
}
```

