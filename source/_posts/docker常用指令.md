---
title: docker常用指令
date: 2019-01-28 11:00:50
tags: docker
categories: docker
---
环境:center os 7.2 阿里云
1. 安装 
```
yum install docker
service docker start
```
2. 随系统启动自动加载
```
chkconfig docker on
```
3. 获取镜像
```
docker pull ubuntu:12.04
```
4. 查看docker版本

```
docker -v
```
5. 启动docker

```
service docker start
```

6. 创建一个容器，让其中运行 bash 应用

```
docker run -t -i ubuntu:12.04 /bin/bash
docker run -t -i training/sinatra /bin/bash
```
7. 退出容器

```
exit
```
8. 列出本地镜像

```
docker images
```
9. 提交更新后的副本

```
docker commit -m "Added json gem" -a "Docker Newbee" e4e1343a73b2 ouruser/sinatra
 -a  可以指定更新的用户信息
 之后是用来创建镜像的容器的 ID
 最后指定目标镜像的仓库名和 tag 信息
 创建成功后会返回这个镜像的 ID信息
```
10. Dockerfile创建镜像

```
语法:
使用 #  来注释
FROM  指令告诉 Docker 使用哪个镜像作为基础
接着是维护者的信息
RUN  开头的指令会在创建中运行，比如安装一个软件包，在这里使用 apt-get 来安装了一些软件

指令: 一条指令相当于一层 一个镜像不能超过 127 层
FROM ubuntu:14.04
MAINTAINER Docker Newbee <newbee@docker.com>
RUN apt-get -qq update
RUN apt-get -qqy install ruby ruby-dev
RUN gem install sinatra

动作:
docker build -t="ouruser/sinatra:v2" .
-t  标记来添加 tag，指定新的镜像的用户信息
“.” 是 Dockerfile 所在的路径（当前目录） 也可以替换为一个具体的 Dockerfile 的路径

```
11. 从本地文件导入镜像

```
cat ubuntu-14.04-x86_64-minimal.tar.gz |docker import - ubuntu:14.04
```

12. 登录到阿里云的docker

```
https://cr.console.aliyun.com/cn-qingdao/repositories 阿里云docker镜像中心
docker login --username=13530471313 registry.cn-hangzhou.aliyuncs.com
```
13. 从阿里云拉取镜像到本地

```
docker pull registry.cn-hangzhou.aliyuncs.com/cloudist/redis
```
14. docker登录鉴权后的数据保存地址

```
docker login docker.io 登录到docker的镜像中心 
wilnd
password
cd /root/.docker/
vim config.json
```
15. 导出镜像到本地文件

```
 docker save
 docker save -o ubuntu_14.04.tar ubuntu:14.04
```

16. 载入镜像
```
docker load --input ubuntu_14.04.tar
```
17. 移除本地镜像

```
docker rmi 
```
18 . 移除容器

```
docker rm
```
19. 启动容器

```
新建并启动:
输出一个 “Hello World”，之后终止容器
docker run ubuntu:14.04 /bin/echo 'Hello world'
启动一个 bash 终端，允许用户进行交互
docker run -t -i ubuntu:14.04 /bin/bash
-t  选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
-i  则让容器的标准输入保持打开
启动容器:
docker start
docker ps -a:查看所有容器，包括停止的

docker attach 进入容器
```
20. 安装nsenter

```
wget https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz
tar -xzvf util-linux-2.24.tar.gz
cd util-linux-2.24/
./configure --without-ncurses
make nsenter
sudo cp nsenter /usr/local/bin
```

21. 私有仓库

```
1. 拉取registry镜像
docker pull registry:2
2. 运行registry
docker run -d -p 5000:5000 --name registry -v /mnt/docker/data/registry:/var/lib/registry registry:2
3. 测试本地registry
3.1 首先为本地镜像打tag
docker tag ubuntu localhost:5000/ubuntu
3.2 查看镜像会发现多出一个镜像：
3.3 push 到registry
docker push localhost:5000/ubuntu
3.4 首先删除刚才创建的镜像
docker rmi localhost:5000/ubuntu
3.5 从registry拉取
docker pull localhost:5000/ubuntu
4. 本地可以查看registry里包含的镜像数据
cd /mnt/docker/data/registry && ls docker/registry/v2/repositories/
5. 查看私有仓库
docker ps -l
```
22. 配置外网可访问registry
```

```
23: 查看

```
正在运行的容器
docker ps
查看所有容器
docker ps -a
```
24. 删除

```
不能够删除一个正在运行的容器，会报错。需要先停止容器。
docker rm [NAME]/[CONTAINER ID]: 
```
25. 终止

```
docker stop [NAME]/[CONTAINER ID]:将容器退出。
docker kill [NAME]/[CONTAINER ID]:强制停止一个容器
```
26. 重启

```
docker restart mymysql
```
















