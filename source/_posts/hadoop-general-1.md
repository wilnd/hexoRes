---
title: hadoop general 1
date: 2017-09-01 23:43:23
tags: hadoop
categories: hadoop
---
这是一片翻译文章，详情请参考[single node](https://hadoop.apache.org/docs/r2.7.4/hadoop-project-dist/hadoop-common/SingleCluster.html)
> 伪分布式(单节点集群)搭建

1. 概述：该文章是用来描述怎么快速的安装和配置一个单节点hadoop，这样可以让你能够快速使用hadoop的 MapReduce以及HDFS组件。
2. 支持平台：GNU和linux是hadoop的开发平台和生产平台，如果使用GNU或者linux系统作为系统节点，节点数可达到2000个。当然hadoop也支持windows系统，这里就不作介绍了。详情请go to [windows](https://wiki.apache.org/hadoop/Hadoop2OnWindows)
3. 软件要求:jdk是必须要安装的，选择oraclejdk1.7+或者openjdk1.7+就可以了。为了方便hadoop脚本管理和调用hadoop的守护进程，ssh也是必须安装的。
4. 软件安装：对于ubantu系统，安装SSH可以这么做，其他系统请自行摸索，如果是center os 7.0+ 可以参考本人前面的博客。
    ```
     sudo apt-get install ssh
    sudo apt-get install rsync
    ```
5. 下载：[可以去这里下载hadoop的镜像](https://www.apache.org/dyn/closer.cgi/hadoop/common/)
6. 准备开始一个hadoop集群的搭建：先将hadoop的分布式系统解压缩，然后编辑etc/hadoop/hadoop-env.sh，加入java的环境变量
    ```
      # set to the root of your Java installation
    export JAVA_HOME=/usr/java/latest
    ```
    执行下面的指令
    ```
    bin/hadoop
    ```
    系统将会展示3haodoop脚本的个使用辅助程序
    ```
    Local (Standalone) Mode
    Pseudo-Distributed Mode
    Fully-Distributed Mode
    ```
    选中其中的任意一个都可以开始你的hadoop集群。
7. 单节点操作：一般的，hadoop被配置运行在非分布式模式中，也就是说是一个独立的java程序，可以用来debbug调试问题。下面创建一个input目录，用来存储所有的xml文件，并作为输入，output文件夹作为输出。
·
mkdir input
cp etc/hadoop/*.xml input
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.4.jar grep input output 'dfs[a-z.]+'
cat output/*
·
8. 伪分布式操作：hadoop可以在伪分布式模式下运行单个节点，在这种情况下，每个hadoop的守护程序都是一个独立的java进程。配置文件如下：

    etc/hadoop/core-site.xml:
    ```
    <configuration>
        <property>
            <name>fs.defaultFS</name>
            <value>hdfs://localhost:9000</value>
        </property>
    </configuration>
    ```
    etc/hadoop/hdfs-site.xml:
    ```
    <configuration>
        <property>
           <name>dfs.replication</name>
           <value>1</value>
         </property>
    </configuration>
    ```
9. 配置伪分布式的ssh
    ```
    ssh localhost
    ```
    如果不需要密码可以直接登ssh到本地执行下面的指令：
    ```
    ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    chmod 0600 ~/.ssh/authorized_keys
    ```
10. 执行：下面的指令是执行本地MapReduce任务的命令
    ```
    1.初始化文件系统
    bin/hdfs namenode -format
    2.启动命名节点守护进程以及数据节点守护进程
    sbin/start-dfs.sh
    hadoop的日志输出目录是 $HADOOP_LOG_DIR所示的值，默认是HADOOP_HOME/logs
    3.打开名字节点的web接口。默认是
    NameNode - https://localhost:50070/
    4.创建HDFS目录，用来执行MapReduce目录
    bin/hdfs dfs -mkdir /user
    bin/hdfs dfs -mkdir /user/<username>
    5.复制输入目录的东西到分布式目录
    bin/hdfs dfs -put etc/hadoop input
    6.运行示例
    bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.4.jar grep input output 'dfs[a-z.]+'
    7.审核输出文件目录，将输出文件的内容从分布式目录里拷贝到本地文件系统里面，然后审核
    bin/hdfs dfs -get output output
    cat output/*
    或者直接在分布式系统里面查看文件
    bin/hdfs dfs -cat output/*
    8.关闭守护陈旭
    sbin/stop-dfs.sh
    ```
11. 单节点的分布式文件系统：通过设置少量参数，额外运行资源管理守护程序，和节点管理守护程序，达到在为分布式模式下分布式文件系统中运行MapReduce工程。接下来的指令是在上一步的1-4点都完成的情况下执行。
    1.配置etc/hadoop/mapred-site.xml文件
     ```<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
	</configuration>    ```
    配置etc/hadoop/yarn-site.xml:文件
    ```
    <configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
    ```
    2.启动资源管理守护程序和数据节点守护程序
    ```
	sbin/start-yarn.sh    
	```
    3.打开资源管理的web接口，默认值是下面的
    ```
	ResourceManager - https://localhost:8088/    
	```
    4.运行mapreduce程序
    5.关闭资源节点
    ```
	sbin/stop-yarn.sh    
	```