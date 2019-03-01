---
title: docker安装redis
date: 2019-01-28 11:00:50
tags: docker
categories: docker
---
1. 查看官方镜像
```
docker search  redis
```
2. 获取官方镜像
```
docker pull  redis
```
3. 查看本地镜像
```
docker images redis
```
4. 运行容器
```
docker run -p 6379:6379 -v $PWD/data:/data  -d  redis redis-server --appendonly yes

docker run 
-p 6379:6379 将容器的6379端口映射到主机的6379端口
-v $PWD/data:/data  将主机中当前目录下的data挂载到容器的/data
-d 
redis-server --appendonly yes  在容器执行redis-server启动命令，并打开redis持久化配置
```
5. 查看容器启动情况
```
docker ps
```
