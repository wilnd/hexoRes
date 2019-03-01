---
title: RabbitMQ可靠投递方案
date: 2018-2-04 11:00:50
tags: java rabbitmq
categories: java
---
1. 场景

```
生产者的消息，往mq服务器投递时，因网络中断、抖动等原因导致投递失败
采取实现ConfirmCallback接口，对消息投递进行确认
若返回true，则认为消息已成功投递；
若返回false，则认为消息投递失败，需要重新投递；
1.exchange存在时,routeKey路由无论是否存在,ack为true
2.exchange不存在时,ack为false。
3.当exchange为null或者空字符串时,ack为true

```
2. 可靠投递具体方案

- 生产者在发送消息之前，生成ack唯一确认id
- 以ackId为键，消息为value，保存进redis缓存
- 实现ConfirmCallback接口
- ConfirmCallback触发回调时，若ack为true，则直接删除此次ackId对应的msg；若ack为false，则走5步骤
- 若ack为false，则将该ackId对应的msg取出，放置进失败的ackFailList中
- 开启定时任务，扫描ackFailList，以一定策略重发失败的msg

 上述方案会产生消息重复发送的问题，考虑到消息发送的速度应尽可能快，才能支持更高的并发。故去重动作，放到了消费端实现。具体方案如下：
3. 消息重复消费处理方案
- 使用业务ID，即businessId，作为消费唯一依据
- 生产者在发送消息时，将businessId、businessBody封装在BusinessMsg中
- 消费者在接收到BusinessMsg后，判断bisinessId是否在已消费集合
- 若已消费集合存在该businessId，则不再消费
- 否则，消费businessId代表的业务消息；消费完毕后，将该businessId保存进已消费集合