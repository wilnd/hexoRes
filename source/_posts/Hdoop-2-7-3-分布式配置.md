---
title: Hdoop 2.7.3 分布式配置
date: 2017-08-29 21:31:19
tags: hadoop
categories: hadoop
---
`
学一门新技术，首先选择官方文档，如果英文水平不够可以找一份中文教程相互印证。碰到疑惑请首先选择google(用google是需要翻墙的)，然后再考虑百度，如果还是解决不了，请加个相关的QQ群，里面会有相关资料。而且可以向群友请教，但是要放宽心态，记住，有人帮忙解答是我的福气，没人帮忙解答是正常情况。
`
>安装虚拟机 CenterOS 7.2

本人使用的虚拟机是VMware。

资源规划

m1： 1G 内存 10G 硬盘

s1：512M 内存 10G硬盘

s2：512M 内存 10G硬盘

>准备好hadoop稳定版。2.7.3是hadoop 2.X的稳定版[hadoop](https://www-eu.apache.org/dist/hadoop/common/stable/)

准备好工具 xshell xftp

通过xftp将下载好的资源放在 /user 下

解压：tar -zxvf hadoop-2.7.3.tar.gz

重命名： mv hadoop-2.7.3.tar.gz hadoop

删除下载包：rm -rf hadoop-2.7.3.tar.gz

>三.配置静态IP（非必须）

查看网卡配
`ip add`
进入网络配置文件目录
`cd /etc/sysconfig/network-scripts`
编辑配置文件，添加修改以下内容
`vim ifcfg-eno16777736`
`
TYPE=Ethernet
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=eno16777736
UUID=a33a7da0-6630-43c3-8b1b-135e2c00f29f
DEVICE=eno16777736
ONBOOT=yes
PEERDNS=yes
PEERROUTES=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPADDR0=192.168.1.111
NETMASK=255.255.255.0
PREFIX0=24
GATEWAY0=192.168.1.1
DNS1=8.8.8.8
`
修改主机名
`hostnamectl set-hostname m1`
网卡重启
`service network restart`
修改时区
`timedatectl set-timezone Asia/Shanghai`
配置局域网映射 x3（ x3表示三个机器都要配置）
`
echo "192.168.1.111 m1" >> /etc/hosts
echo "192.168.1.112 s2" >> /etc/hosts
echo "192.168.1.113 s3" >> /etc/hosts
`
创建 hadoop 用户以及 hadoop 用户组 x3
`
groupadd hadoop
useradd -m -g hadoop hadoop
passwd hadoop
`
>配置jdk。

查看当前系统是否安装了jdk
`rpm -qa | grep jdk`
卸载
`
rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.65-3.b17.el7.x86_64
rpm -e --nodeps java-1.8.0-openjdk-1.8.0.65-3.b17.el7.x86_64
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.91-2.6.2.3.el7.x86_64
rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.91-2.6.2.3.el7.x86_64
`
下载jdk （去官网下载即可），下载好后将jdk放在/user目录下
解压缩
`tar -zxvf jdk-8u131-linux-x64.tar.gz`
删除下载包
`rm -rf jdk-8u131-linux-x64.tar.gz`
配置环境变量
`vim /etc/profile`
在最下方加上这么几句
`
export JAVA_HOME=/usr/jdk1.8.0_131/
export JRE_HOME=/usr/jdk1.8.0_131/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin 
`
使环境变量生效
`
source /etc/profile
`
查看是否生效
`java -version`

>SSH免密登录

配置ssh环境变量（x3 表示三个节点都要做此动作）
`echo 'eval $(ssh-agent)' >> /etc/profile`
刷新环境变量（x3）
`source /etc/profile`
切换到hadoop用户组（x3）
`su hadoop`
ssh初始化 （x3）
`ssh localhost`
进入hadoop用户目录（m1 表示只有m1节点做此动作）
`cd ~/.ssh`
利用 ssh-keygen 生成密钥 然后一直按回车（m1）
ssh-keygen -t rsa -P ""
将id_rsa.pub的内容追加到了authorized_keys的内容后面 （m1）
`cat id_rsa.pub >> authorized_keys`
将公钥copy到s2 会要输入s2 hadoop用户的登录密码（m1）
`ssh-copy-id s2`
将公钥copy到s3 会要输入s3 hadoop用户的登录密码（m1）
`ssh-copy-id s3`
使用 ssh-agent 实现免密登录（m1）
`ssh-add ~/.ssh/id_rsa`
修改权限 ~/.ssh/*目录的权限(x3)
`
chmod 600 ~/.ssh/authorized_keys
`
检查免密登录是否成功(m1)
`ssh s2`

>安装hadoop 2.7.3 （x3）

选择hadoop的安装地址(x3)
`cd /user`
解压
`tar -zxvf hadoop-2.7.3.tar.gz`
重命名(x3)
`mv hadoop-2.7.3/ hadoop`
赋予权限 令hadoop用户组下的hadoop用户对/user/hadoop 及以下所有子目录拥有管理员权限
`chown -R hadoop:hadoop hadoop（x3）`
在/user/hadoop目录下创建几个目录，用来放临时文件
`mkdir dfs
mkdir dfs/name
mkdir dfs/data
mkdir tmp`
配置配置文件 都在/usr/hadoop/etc/hadoop目录下
`
hadoop-env.sh
yarn-env.sh
slaves
core-site.xml
hdfs-site.xml
mapred-site.xml
yarn-site.xml
hadoop-env.sh 配置hadoop的java环境变量 export JAVA_HOME=/usr/jdk1.8.0_131
yarn-env.sh 配置yarm的java环境变量 export JAVA_HOME=/usr/jdk1.8.0_131
`
slaves slaves下的所有机器都是当前name node 的data node 如果是伪分布式配置localhost，我这边配置的是s2 和s3 
core-site.xml 配置hadoop的一些重要参数，包括缓存文件的保存路径，hdfs文件系统的入口
hdfs-site.xml 配置hdfs相关内容，包括备用节点，name node ， data node的存储文件的位置
mapred-site.xml 配置mapreduce相关的东西
yarn-site.xml 配置yarn相关的东西
配置hadoop环境变量（x3）
`vim /etc/profile`
`#hadoop
export HADOOP_HOME=/user/hadoop
export PATH=$PATH:$HADOOP_HOME/sbin
export PATH=$PATH:$HADOOP_HOME/bin`
使立即生效
`source /etc/profile`
到此，主节点已配置完全，从节点可参照主节点进行配置，下面这个可以将主节点属于hadoop的配置参数直接拷贝到从节点
`scp -r /usr/hadoop/etc/hadoop/ root@s2:/usr/hadoop/etc
scp -r /usr/hadoop/etc/hadoop/ root@s3:/usr/hadoop/etc`
hadoop初始化 (m1节点)
bin/hdfs namenode -format
启动hadoop集群(m1节点）
start-all.sh
[hadoop常用参数](https://www.zhangrenhua.com/2016/01/05/hadoop-%E5%B8%B8%E7%94%A8%E5%8F%82%E6%95%B0%E6%95%B4%E7%90%86/?spm=5176.100239.blogcont152086.23.2GzKob)
[hadoop默认参数](https://segmentfault.com/a/1190000000709725?spm=5176.100239.blogcont152086.24.58jMXV)
