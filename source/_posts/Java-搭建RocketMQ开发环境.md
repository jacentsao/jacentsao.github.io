---
title: Java - 搭建RocketMQ开发环境
date: 2018-12-04 16:34:43
tags: [后端]
categories: 后端
---

今天尝试搭建一下RocketMQ开发环境，特此记录下搭建流程和搭建过程中遇到的一些问题。

首先根据官网的[教程](https://rocketmq.apache.org/docs/quick-start/)搭建。

### 快速启动

#### 环境准备

- 64bit OS, Linux/Unix/Mac is recommended;
- 64bit JDK 1.8+;
- Maven 3.2.x;
- Git;
- 4g+ free disk for Broker server

#### 下载，构建

下载源码或者release的版本，下载地址: [https://www.apache.org/dyn/closer.cgi?path=rocketmq/4.3.2/rocketmq-all-4.3.2-source-release.zip](https://www.apache.org/dyn/closer.cgi?path=rocketmq/4.3.2/rocketmq-all-4.3.2-source-release.zip)

如果下载的是源码的话需要使用maven进行编译：

```shell
	  
	> unzip rocketmq-all-4.3.2-source-release.zip
	> cd rocketmq-all-4.3.2/
	> mvn -Prelease-all -DskipTests clean install -U
	> cd distribution/target/apache-rocketmq
  
```

如果不是源码的话直接进入解压后的文件即可。

#### 启动Name Server

```shell

	> nohup sh bin/mqnamesrv &
	> tail -f ~/logs/rocketmqlogs/namesrv.log
	The Name Server boot success...
	以上是官网提供的示例。我遇到的情况是输出倒了当前目录下的nohup.out文件中，使用tail 或者vim确定Name Server是否正常启动
```

#### 启动Broker

```shell
	> nohup sh bin/mqbroker -n localhost:9876 &
	> tail -f ~/logs/rocketmqlogs/broker.log 
	The broker[%s, 172.30.30.233:10911] boot success...
	同样的在nohub.out中查看
```

#### 发送和接收消息

```shell
	//告诉客户端需要发送到哪个服务端
	> export NAMESRV_ADDR=localhost:9876
	> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
	SendResult [sendStatus=SEND_OK, msgId= ...
	
	> sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
	ConsumeMessageThread_%d Receive New Messages: [MessageExt...
```

#### 关闭服务

```shell
	> sh bin/mqshutdown broker
	The mqbroker(36695) is running...
	Send shutdown request to mqbroker(36695) OK
	
	> sh bin/mqshutdown namesrv
	The mqnamesrv(36664) is running...
	Send shutdown request to mqnamesrv(36664) OK

```

### 简单的示例

官网地址[https://rocketmq.apache.org/docs/simple-example/](https://rocketmq.apache.org/docs/simple-example/)

#### 1.添加依赖

由于我们使用的是maven管理工具,gradle的依赖可以参考官网。

```xml
	<dependency>
	    <groupId>org.apache.rocketmq</groupId>
	    <artifactId>rocketmq-client</artifactId>
	    <version>4.3.0</version>
	</dependency>
```

#### 2.1 发送同步消息

同步消息适合于很多场景，例如重要消息的通知，SMS通知，SMS系统市场。其他都在官网，可以多多看看文档。

```java
public class SyncProducer {
    public static void main(String[] args) throws Exception {
        //Instantiate with a producer group name.
        DefaultMQProducer producer = new
            DefaultMQProducer("please_rename_unique_group_name");
        // Specify name server addresses.
        producer.setNamesrvAddr("localhost:9876");
        //Launch the instance.
        producer.start();
        for (int i = 0; i < 100; i++) {
            //Create a message instance, specifying topic, tag and message body.
            Message msg = new Message("TopicTest" /* Topic */,
                "TagA" /* Tag */,
                ("Hello RocketMQ " +
                    i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */
            );
            //Call send message to deliver message to one of brokers.
            SendResult sendResult = producer.send(msg);
            System.out.printf("%s%n", sendResult);
        }
        //Shut down once the producer instance is not longer in use.
        producer.shutdown();
    }
}
```

#### 消费

```java
public class Consumer {

    public static void main(String[] args) throws InterruptedException, MQClientException {

        // Instantiate with specified consumer group name.
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name");
         
        // Specify name server addresses.
        consumer.setNamesrvAddr("localhost:9876");
        
        // Subscribe one more more topics to consume.
        consumer.subscribe("TopicTest", "*");
        // Register callback to execute on arrival of messages fetched from brokers.
        consumer.registerMessageListener(new MessageListenerConcurrently() {

            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                ConsumeConcurrentlyContext context) {
                System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), msgs);
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        //Launch the consumer instance.
        consumer.start();

        System.out.printf("Consumer Started.%n");
    }
}
```

### 遇到的问题


1. `error='Cannot allocate memory'`无法分配内存问题，报错信息如下：

```shell
Java HotSpot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00000005fb000000, 8589934592, 0) failed; error='Cannot allocate memory' (errno=12)
#
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (malloc) failed to allocate 8589934592 bytes for committing reserved memory.
# An error report file with more information is saved as:
# /usr/local/mq/rocketmq-all-4.3.2-bin-release/hs_err_pid6845.log
Unrecognized VM option 'MetaspaceSize=128m'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

将bin目录下的runbroker.sh的虚拟机配置调整如下：

```shell
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn125m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

同样的如果nameserver有问题也要相应的进行调整。

2. 在客户机访问服务器mq的时候出现`Exception in thread "main" org.apache.rocketmq.remoting.exception.RemotingTooMuchRequestException: sendDefaultImpl call timeout`，此时时由于RocktMQ没有绑定网卡ip导致。调整启动broker的配置如下所示：

```shell
//生成配置信息
echo "brokerIP1=10.2.x.x" > broker.properties

//broker绑定ip
nohup sh bin/mqbroker -n 10.2.x.x:9876 -c ./broker.properties autoCreateTopicEnable=true &
```

3. Java版本太低出现报错，此时需要升级到Java8，或者将该配置移除：

```shell
Unrecognized VM option 'MetaspaceSize=128m'
Error: Could not create the Java Virtual Machine.
```

移除以后即：

```shell
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn125m"
```

4. 如果一直出现`No route info of this topic`，请检查服务器端口是否是开放的，另外可以试一下关闭客户端的防火墙再去请求试试。