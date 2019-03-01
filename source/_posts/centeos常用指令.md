---
title: centeos常用指令
date: 2018-2-14 11:00:50
tags: linux centeros
categories: linux
---
1. 更换yum源

```
1. su 上升到root权限
2. cd  /etc/yum.repos.d/ 打开镜像文件夹
3. wget  http://mirrors.aliyun.com/repo/Centos-7.repo 下载阿里云镜像
4. mv  CentOS-Base.repo CentOS-Base.repo.bak 备份原镜像文件
5. mv Centos-7.repo CentOS-Base.repo -y 替换系统镜像文件
6. yum源更新 
yum clean all  
yum makecache -y 
yum update -y


```
2. 查看某个端口使用情况

```
yum install net-tools 
yum install lsof -y
lsof -i:端口号

```
3. 赋权限

```
chown -R git:git
```


4.1 安装jdk

```
yum list java-1.8*
yum install java-1.8.0-openjdk* -y
java -version
```
4.2 查看java的安装目录

```
which java
ls -lrt /usr/bin/java
ls -lrt /etc/alternatives/java
cd /usr/lib/jvm
```

4.3 安装git

```
1. 安装依赖
sudo yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
2. 在相应路径下载git
wget https://github.com/git/git/archive/v2.3.0.zip
3. 解压git
yum install -y unzip zip
unzip v2.3.0 -d git
```
5.1 安装jdk

```
进官网
https://www.oracle.com/technetwork/java/javase/downloads/index.html
下载对应版本jdk
传输到服务器解压
配置java_home
export JAVA_HOME=/usr/lib/jvm/jre-1.7.0
```
5.2 安装git

```
1. 安装依赖
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
2. 移除旧版本的git
yum remove git
3. 下载git压缩包
wget https://github.com/git/git/archive/v2.3.0.zip
4. 解压git
unzip v2.3.0 -d git 
5. 到下载目录编译git源码
make prefix=/usr/local/git all
6. 安装git到指定目录
make prefix=/usr/local/git install
7. 配置git
vim /etc/profile
在末尾添加
export PATH=/usr/local/git/bin:$PATH
8. 使用source命令试用修改
source /etc/profile
9. 检查git版本
git --version
```
5.3 安装maven

```
下载maven
wget http://mirrors.shu.edu.cn/apache/maven/maven-3/3.5.3/binaries/apache-maven-3.5.3-bin.tar.gz
# maven所在的目录 
export M2_HOME=/usr/local/maven/apache-maven-3.3.9
# maven bin所在的目录
export M2=$M2_HOME/bin
# 将maven bin加到PATH变量中  
export PATH=$M2:$PATH
# 配置JAVA_HOME所在的目录，注意这个目录下应该有bin文件夹，bin下应该有java等命令 
export JAVA_HOME=/usr/lib/jvm/jre-1.7.0
#应用修改
source ~/.bash_profile
#查看是否成功
mvn -version
```
6. 安装jenckins

```
1. yum的repos中默认是没有Jenkins的，需要先将Jenkins存储库添加到yum repos。
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
2. yum安装
yum install jenkins
3. 修改jenckins配置文件
vim /etc/sysconfig/jenkins
4. 修改用户和端口号
JENKINS_USER="root"
JENKINS_PORT="8081"
5. 重启
service jenkins start
6. 找秘钥
cat /var/lib/jenkins/secrets/initialAdminPassword
```
7. 查看系统情况
```
1. 查看内存使用情况
free -h 　free -m
2. 查看CPU核心数
cat /proc/cpuinfo| grep "cpu cores"| uniq 
3. 查看当前文件夹下所有文件大小(包括子文件)
du -sh
4. 查看cpu使用情况
top
5. 查看端口使用情况
lsof -i:端口号
netstat -apn|grep 端口号
6. 

```
8. 

