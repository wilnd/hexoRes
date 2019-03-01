---
title: RabbitMQ RabbitTemplate API
date: 2018-2-04 11:00:50
tags: java rabbitmq
categories: java
---
1. addListener(com.rabbitmq.client.Channel channel)
```
将渠道作为监听者
```
2. convertAndSend(java.lang.Object object)
```
将java对象转换为AMQP消息,使用默认的路由key发送到默认的交换机
```
3. convertAndSend(java.lang.String exchange, java.lang.String routingKey, java.lang.Object message, MessagePostProcessor messagePostProcessor, CorrelationData correlationData)
```
将java对象转换为AMQP消息,
使用指定的路由key发送到
指定的交换机
信息在发送前,可以使用messagePostProcessor进行处理
并指定correlationData
```
4. convertMessageIfNecessary(java.lang.Object object) 
```
文档没有太多注释,估计是在持久化的时候有用到
```
5. 	convertSendAndReceive(java.lang.Object message)

```
rpc部分
将java对象转换为消息,通过默认的路由,转换给默认的交换机,并尝试接收响应,再转换为java对象.
实现类会自动设置reply-to头到一个特定的队列,这个队列会有一些超时设置
```
6. convertSendAndReceiveAsType(java.lang.Object message, CorrelationData correlationData, org.springframework.core.ParameterizedTypeReference<T> responseType)

```
将java对象转换为消息,通过默认的路由,转换给默认的交换机,并尝试接收响应,再转换为指定类型的java对象.
实现类会自动设置reply-to头到一个特定的队列,这个队列会有一些超时设置
```

7. determineConfirmsReturnsCapability(ConnectionFactory connectionFactory) 
8. doReceiveNoWait(java.lang.String queueName)

```
非阻塞的接收消息
```
9. doSend(com.rabbitmq.client.Channel channel, java.lang.String exchangeArg, java.lang.String routingKeyArg, Message message, boolean mandatory, CorrelationData correlationData)

```
发送消息给特定的交换机
```
10. doStart()

```
执行额外的开始动作
```
11. doStop()

```
执行额外的停止动作
```
12. execute(ChannelCallback<T> action)

```
执行一个带回调的通道,并且在之后能可靠的关闭这个通道
```
13. expectedQueueNames()

```
在容器启动的时候被调用,用来确定队列被正确的配置
```
14. getEncoding()

```
在string跟byte数组建转换的时候,经常需要知道编码方式
```
15. getExchange() 

```
获取交换机
```
16. getMessageConverter()

```
获取消息转换器
```
17. getMessagePropertiesConverter()

```
获取参数转换器
```
18. getRoutingKey() 

```
获取路由key的名称
```
19. getUnconfirmed(long age)

```
获取超期的还未确认的correlation data , 并将他们移除
```
20. getUnconfirmedCount()

```
获取未确认消息的数量
```
21. getUUID()
22. handleConfirm(PendingConfirm pendingConfirm, boolean ack)

```
确认收到后被通道调用
```
23. handleReturn(int replyCode, java.lang.String replyText, java.lang.String exchange, java.lang.String routingKey, com.rabbitmq.client.AMQP.BasicProperties properties, byte[] body) 
24. initDefaultStrategies()

```
设置默认策略
```
25. invoke(RabbitOperations.OperationsCallback<T> action, com.rabbitmq.client.ConfirmCallback acks, com.rabbitmq.client.ConfirmCallback nacks)

```
在同一个通道里面,进行调用操作
```
26. isChannelLocallyTransacted(com.rabbitmq.client.Channel channel)

```
检查给定的通道在本地被处理,是否模板的通道处理器正在管理这些通道,是否没有其他处理器在处理这个通道
```
27. isConfirmListener() 
28. isMandatoryFor(Message message)
29. isReturnListener() 
30. isRunning() 
31. isUsePublisherConnection()
32. onMessage(Message message) 
33. receive()

```
从默认队列接收消息
```
34. receiveAndConvert()

```
从默认队列接收消息,并转化成java对象
```
35. revoke(com.rabbitmq.client.Channel channel)

```
被调用的时候,渠道会移除所有监听器的参考内容,这些监听器不会再被渠道调用
```
36. send(Message message)

```
使用默认的路由key,发送消息到默认的交换机
```
37. sendAndReceive(Message message)

```
rpc调用
```
38. setAfterReceivePostProcessors(MessagePostProcessor... afterReceivePostProcessors)

```
设置消息处理器,这个动作在通道被调用或任何消息转化执行前立即调用
```
39. setBeanFactory
40. setBeanName(java.lang.String name) 
41. setBeforePublishPostProcessors(MessagePostProcessor... beforePublishPostProcessors)
42. setConfirmCallback(RabbitTemplate.ConfirmCallback confirmCallback) 43. setCorrelationKey(java.lang.String correlationKey)
44. setCorrelationKey(java.lang.String correlationKey)

```
设置correlation data
```
45. setDefaultReceiveQueue(java.lang.String queue)

```
设置默认的接收队列
```
46.	setEncoding(java.lang.String encoding)

```
设置默认的编码方式
```
47. setExchange(java.lang.String exchange)
```
设置默认的交换机,在调用时未指定交换机时使用
```
48. setMandatory(boolean mandatory)

```
发送消息时,设置必填标志,仅仅在有回调时可申请
```
49. setNoLocalReplyConsumer(boolean noLocalReplyConsumer)
50. setQueue(java.lang.String queue)	

```
设置队列
```
51. setReceiveConnectionFactorySelectorExpression(org.springframework.expression.Expression receiveConnectionFactorySelectorExpression)
52. setReceiveTimeout(long receiveTimeout)

```
设置接收超时时间
```
53. setRecoveryCallback(org.springframework.retry.RecoveryCallback<?> recoveryCallback)
54. setRoutingKey(java.lang.String routingKey)

```
设置路由key
```
55. setSendConnectionFactorySelectorExpression(org.springframework.expression.Expression sendConnectionFactorySelectorExpression)
56. setTaskExecutor(java.util.concurrent.Executor taskExecutor)
57. setUseDirectReplyToContainer(boolean useDirectReplyToContainer)
58. setUsePublisherConnection(boolean usePublisherConnection)
59. setUserCorrelationId(boolean userCorrelationId)
60. setUserIdExpression(org.springframework.expression.Expression userIdExpression)
61. setUseTemporaryReplyQueues(boolean value)
62. start() 
63. stop() 
64. useDirectReplyTo()
65. waitForConfirms(long timeout)
66. waitForConfirmsOrDie(long timeout)
