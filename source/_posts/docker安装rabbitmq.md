---
title: docker安装rabbitmq
date: 2019-01-28 11:00:50
tags: docker
categories: docker
---
1. 下载镜像
```
docker pull rabbitmq:3.7.7-management
```
2. 运行RabbitMQ

```
docker run -d --hostname myrabbit --name rabbit -p 15672:15672 -p 5672:5672 rabbitmq:3.7.7-management

-d 后台运行
--hostname 主机名称
--name 容器名称
-p 15672:15672 http访问端口，映射本地端口到容器端口
-p 5672:5672 amqp端口，映射本地端口到容器端口
```

