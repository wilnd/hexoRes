---
title: flume日志多线程下压力测试
date: 2017-09-11 11:00:50
tags: hadoop
categories: hadoop
---
#### 实际工作中会要用到在多线程的情况下对程序进行压力测试。实现思路是
#####    1. 从外部传入两个参数，代表要开启的线程数以及每个线程要传输的数据条数。如果外界没有传入取默认值。
#####    2. 设置一个线程计数器，CountDownLatch，初始化值为传入的线程个数。并在主线程结束前阻塞主线程的完成。
#####    3. 设置一个线程池，线程池循环生成线程。每个线程执行各自的数据传输事项，并在线程执行完后将计数器减一。
#####    4. 输出线程执行的情况
    
#### pom配置

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>jsonflume</groupId>
    <artifactId>flume</artifactId>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <log4j.version>2.8.2</log4j.version>
        <slf4j.version>2.8.2</slf4j.version>
        <flume-ng.versiopn>2.8.2</flume-ng.versiopn>
        <jackson.version>2.7.0</jackson.version>
    </properties>

    <dependencies>
        <!-- log4j -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <!-- slf4j -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-slf4j-impl</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <!-- flume -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-flume-ng</artifactId>
            <version>${flume-ng.versiopn}</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.7.0</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>1.4</version>
                <configuration>
                    <createDependencyReducedPom>true</createDependencyReducedPom>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer
                                        implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>LaoutTest</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>


</project>
```

#### log4j2配置

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <!--自定义flume日志级别-->
    <!--<CustomLevels>-->
        <!--<CustomLevel name="FLUME" intLevel="88" />-->
    <!--</CustomLevels>-->
    <!--定义输出日志的地方-->
    <Appenders>
        <!--控制台输出-->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %-7level %logger{36} - %msg%n"/>
        </Console>
        <!--log文件输出-->
        <File name="MyFile" fileName="logs/app.log">
            <PatternLayout pattern="%d %-7level %logger{36} - %msg%n"/>
        </File>
        <!--输出到flume-->
        <!--<Flume name="eventLogger" compress="false">-->
            <!--<Agent host="192.168.1.111" port="41414"/>-->
            <!--&lt;!&ndash;输出方式为json&ndash;&gt;-->
            <!--<JSONLayout/>-->
        <!--</Flume>-->
    </Appenders>
    <!--配置不同的日志级别输出到不同地点-->
    <Loggers>
        <!--root代表默认日志级别-->
        <Root level="error">
            <!--设定flume级别及以上的日志通过flume-appender输出-->
            <!--<AppenderRef ref="eventLogger" level="FLUME" />-->
            <!--设定console级别及以上的日志通过控制台输出-->
            <AppenderRef ref="Console" level="info" />
            <!--设定error及以上的日志通过log文件输出-->
            <AppenderRef ref="MyFile" level="error" />
        </Root>
    </Loggers>
</Configuration>
```

#### 压力测试

```
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.apache.commons.lang.StringUtils;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * Created by hadoop on 2017/7/28.
 */
public class LaoutTest {
    static Logger logger = LogManager.getLogger(LaoutTest.class);
    public static void main(String[] args) throws InterruptedException {
        //初始化参数
        int num= 100000;
        int threadNum =10;
        //args.length>0表示有参数传递进来，那么num的值为第一个参数，表示每个线程发送的日志次数 threadNum为第二个参数，表示要测试的线程个数
        if (args.length >0 ){
            if (StringUtils.isNotEmpty(args[0])){
                num = Integer.valueOf(args[0]);
            }
            if (StringUtils.isNotEmpty(args[1])){
                threadNum = Integer.valueOf(args[1]);
            }
        }
        //CountDownLatch是一个线程计数器，初始化一个可以计数threadNum个线程的线程计数器
        CountDownLatch countDownLatch = new CountDownLatch(threadNum);
        //获取开始时间
        long time1 = new Date().getTime();
        //创建线程池
        ExecutorService executor = Executors.newFixedThreadPool(threadNum);
        for (int i=0;i<threadNum;i++){
            executor.execute(new FlumeThread(countDownLatch,num));
        }
        try{
            // 阻塞当前线程，直到倒数计数器倒数到0
            countDownLatch.await();
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        //获取结束时间
        long time2 = new Date().getTime();
        //对时间进行处理
        SimpleDateFormat formatter = new SimpleDateFormat ("yyyy.MM.dd hh:mm:ss z");
        String startTime = formatter.format(time1);
        String endTime = formatter.format(time2);
        double interval = (time2-time1)/1000;
        double numPerSecond = (num+1.0)*threadNum/interval;
        System.out.println(threadNum +"个线程"+"每个线程"+num+"条数据，共"+num*threadNum/10000+"W 条数据耗时-----"+interval+"秒.平均每秒传递收集"+numPerSecond+"条数据实验开始时间-----"+startTime+"结束时间-----"+endTime);
    }
}

/**
 * describe:flume线程实现类，执行num次flume日志打印，线程执行完线程计数器减一
 *
 */
class FlumeThread implements Runnable{
    static Logger logger = LogManager.getLogger(FlumeThread.class);
    private  int num;
    private CountDownLatch countDownLatch;
    FlumeThread(CountDownLatch countDownLatch,int num){
        this.countDownLatch = countDownLatch;
        this.num = num;
    }
    public void run() {
        int i=0;
        while (i<num) {
            i++;
            logger.log(Level.getLevel("FLUME"), "LFH01|some message");
        }
        // 倒数器减1
        countDownLatch.countDown();
    }
}



```