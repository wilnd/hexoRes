---
title: redis 笔记
date: 2018-2-14 11:00:50
tags: java redis
categories: java
---
1. windows下下载
```
https://github.com/MSOpenTech/redis/releases
```
2. windows下运行
```
redis-server.exe redis.windows.conf
```
3. 使用密码连接redis
```
windows:
redis-cli.exe --raw -h 127.0.0.1 -p 6379 -a Passw0rd 
linux:
./redis-cli --raw -h 127.0.0.1 -p 6379 -a Passw0rd 

redis-cli.exe --raw -h 101.132.69.39 -p 6379 -a mustangP@51 
./redis-cli --raw -h 101.132.69.39 -p 6379 -a mustangP@51

```
4. 查看配置文件
```
CONFIG GET CONFIG_SETTING_NAME
实例:
4.1 CONFIG GET loglevel
4.2 CONFIG GET *
```
5. 编辑配置文件
```
CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE
实例:
5.1 CONFIG SET loglevel "notice"
```
6. 配置 redis 外网可访问
```
由于 redis 采用的安全策略，默认会只准许本地访问。需要通过简单配置，完成允许外网访问.
修改 redis 的配置文件，将所有 bind 信息全部屏蔽。
修改完成后，需要重新启动 redis 服务。
修改 Linux 的防火墙(iptables)，开启你的 redis 服务端口，默认是 6379。
```
7. 有时候会有中文乱码。后面加上 --raw
```
redis-cli --raw
```
8. 键命令
```
基本语法:
COMMAND KEY_NAME
详情见另一个md
```
9. 序列化

```
任何存储都需要序列化。只不过常规你在用DB一类存储的时候，这个事情DB帮你在内部搞定了（直接把SQL带有类型的数据转换成内部序列化的格式，存储；读取时再解析出来）。
而Redis并不会帮你做这个事情。当你用Redis的key和value时，value对于redis来讲就是个byte array。你要自己负责把你的数据结构转换成byte array，等读取时再读出来。
简单的来说,要形成一个序列化的约定，确保存进去的东西能解析回来不出错
```
10. redis数据类型

![微信截图_20190108105141](2F9E8189E3B34EA7829EED65A704AAD5)

11. redis数据库的概念

```
1. redis在单机的情况下会有多个数据库,如果是集群就没有数据库的概念
2. redis支持多个数据库,并且每个数据库的数据不能共享
3. redis的数据库对外看都是从0开始命名.redis默认支持16个数据库,可以通过配置无上限递增.
4. 客户端跟redis连接后默认会连接0号数据库,不过可以随时使用select命令更换数据库.
5. redis不支持自定义为数据库命名
6. 开发者必须自己清楚哪个数据库存了哪些数据
7. redis所有数据库共用一套权限,要么有所有数据库的访问权限,要么没有权限
8. 
```
12. redis事务

```
MULTI 开启一个事务
命令1
命令2
……
EXEC 提交事务
```
13. 服务器命令
```
INFO 获取服务器的统计信息
```
14. redis数据备份
```
SAVE
在 redis 安装目录中创建dump.rdb文件
BGSAVE
在后台执行备份文件
```
15. 数据恢复
```
1. 备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务
2. 获取 redis 目录中的文件
CONFIG GET dir
```
16. redis安全
查看是否设置了密码验证:
```
CONFIG get requirepass
```
默认情况下 requirepass 参数是空的，这就意味着你无需通过密码验证就可以连接到 redis 服务。
你可以通过以下命令来修改该参数：
```
CONFIG set requirepass "runoob"
```
设置密码后，客户端连接 redis 服务就需要密码验证，否则无法执行命令。
17. 性能测试

```
redis-benchmark [option] [option value]
```
18. 最大连接数

Redis 通过监听一个 TCP 端口或者 Unix socket 的方式来接收来自客户端的连接，当一个连接建立后，Redis 内部会进行以下一些操作
- 客户端 socket 会被设置为非阻塞模式，因为 Redis 在网络事件处理上采用的是非阻塞多路复用模型
- 这个 socket 设置 TCP_NODELAY 属性，禁用 Nagle 算法
- 创建一个可读的文件事件用于监听这个客户端 socket 的数据发送    
```
CLIENT LIST 返回连接到 redis 服务的客户端列表
CLIENT SETNAME 设置当前连接的名称
CLIENT GETNAME 获取通过 CLIENT SETNAME 命令设置的服务名称
CLIENT PAUSE 挂起客户端连接，指定挂起的时间以毫秒计
CLIENT KILL 关闭客户端连接
```
19. 管道技术

Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。这意味着通常情况下一个请求会遵循以下步骤：
- 客户端向服务端发送一个查询请求，并监听Socket返回，通常是以阻塞模式，等待服务端响应.
- 服务端处理命令，并将结果返回给客户端。
- 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。

20. 分区

分割数据到多个redis实例.
- 范围分区
- 哈希分区

