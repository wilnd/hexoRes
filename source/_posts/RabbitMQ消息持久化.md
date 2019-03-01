---
title: RabbitMQ可靠投递方案
date: 2018-2-04 11:00:50
tags: java rabbitmq
categories: java
---
要从奔溃的 RabbitMQ 中恢复的消息，我们需要做消息持久化。如果消息要从 RabbitMQ 奔溃中恢复，那么必须满足三点，且三者缺一不可。
1. 交换器必须是持久化。
```
原生的实现方式:
// 参数1 exchange ：交换器名
// 参数2 type ：交换器类型
// 参数3 durable ：是否持久化
channel.exchangeDeclare(EXCHANGE_NAME, "topic", true);

Spring AMQP 的实现方式:
// 参数1 name ：交互器名
// 参数2 durable ：是否持久化
// 参数3 autoDelete ：当所有消费客户端连接断开后，是否自动删除队列
new TopicExchange(name, durable, autoDelete)
```
2. 队列必须是持久化的。

```
原生的实现方式:
// 参数1 queue ：队列名
// 参数2 durable ：是否持久化
// 参数3 exclusive ：仅创建者可以使用的私有队列，断开后自动删除
// 参数4 autoDelete : 当所有消费客户端连接断开后，是否自动删除队列
// 参数5 arguments
channel.queueDeclare(QUEUE_NAME, true, false, false, null);

Spring AMQP 的实现方式:
// 参数1 name ：队列名
// 参数2 durable ：是否持久化
// 参数3 exclusive ：仅创建者可以使用的私有队列，断开后自动删除
// 参数4 autoDelete : 当所有消费客户端连接断开后，是否自动删除队列
new Queue(name, durable, exclusive, autoDelete);

```

3. 消息必须是持久化的。

```
原生的实现方式:
// 参数1 exchange ：交换器
// 参数2 routingKey ： 路由键
// 参数3 props ： 消息的其他参数,其中 MessageProperties.PERSISTENT_TEXT_PLAIN 表示持久化
// 参数4 body ： 消息体
channel.basicPublish("", queue_name, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());

Spring AMQP 的实现方式:
rabbitTemplate.convertAndSend(exchange, routeKey, message);

```
