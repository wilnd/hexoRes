---
title: RabbitMQ笔记
date: 2018-2-04 11:00:50
tags: java rabbitmq
categories: java
---
1. 下载
```
1.安装所需依赖的安装包
yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel
2. 先下载安装RabbitMQ所需的的依赖软件erlang
2.1 wget http://distfiles.macports.org/erlang/otp_src_20.3.tar.gz
2.2 tar zxvf otp_src_20.3.tar.gz
2.3 mkdir -p /data/local/erlang
2.4 cd otp_src_20.3
2.5 ./configure --prefix=/data/local/erlang --with-ssl --enable-threads --enable-smmp-support --enable-kernel-poll --enable-hipe --without-javac
2.6 make && make install
2.7 echo "export PATH=$PATH:/data/local/erlang/bin" >> /etc/profile
2.8 source /etc/profile
2.9 erl -version
3. 下载安装RabbitMQ二进制包
3.1 wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.7/rabbitmq-server-3.6.7-1.suse.noarch.rpm
3.2 rpm --import https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
3.3 yum install rabbitmq-server-3.6.7-1.suse.noarch.rpm
4. 调整系统限制
4.1 vi /etc/sysctl.conf
fs.file-max = 100000
4.2 使设置生效
sysctl -p
4.3 查看系统限制
sysctl fs.file-max
4.4 调整用户限制
vi /etc/security/limits.conf
4.5 重启系统使之生效，检查用户限制是否生效
ulimit -n
5. 开通防火墙上Web UI访问端口（默认：15672/tcp）
firewall-cmd --permanent --add-port=15672/tcp
firewall-cmd –-reload
5.1 设置RabbitMQ服务自启动，并启动RabbbitMQ服务
chkconfig rabbitmq-server on
service rabbitmq-server start
5.2 启用RabbitMQ监控插件
rabbitmq-plugins enable rabbitmq_management

```
2. 安装

```
yum install rabbitmq-server-3.6.6-1.el7.noarch.rpm 
```
3. 常见操作

```
# 添加开机启动RabbitMQ服务
 sudo chkconfig rabbitmq-server on  
# 启动服务
 /sbin/service rabbitmq-server start 
# 查看服务状态
 /sbin/service rabbitmq-server status  
# 停止服务
 /sbin/service rabbitmq-server stop   
 
# 查看当前所有用户
 rabbitmqctl list_users
 
# 查看默认guest用户的权限
 rabbitmqctl list_user_permissions guest
 
# 由于RabbitMQ默认的账号用户名和密码都是guest。为了安全起见, 先删掉默认用户
 rabbitmqctl delete_user guest
 
# 添加新用户
 rabbitmqctl add_user username password
 
# 设置用户tag
 rabbitmqctl set_user_tags username administrator
 
# 赋予用户默认vhost的全部操作权限
 rabbitmqctl set_permissions -p / username ".*" ".*" ".*"
 
# 查看用户的权限
 rabbitmqctl list_user_permissions username
```
4. 默认端口

```
15672
```

