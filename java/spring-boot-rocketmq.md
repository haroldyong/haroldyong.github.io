### Rocketmq使用的一些经验


## 1. spring-boot

 spring boot 2.1.0 之前有一个bug，selectorExpression 无法从配置中心获取，但可以使用常量方式

 `
 @Service
 @RocketMQMessageListener(topic = "${dingding.alert.topic}", consumerGroup = "${rocketmq.consumer.group}",
         selectorExpression = "${dingding.alert.tags}")
 public class DingdingAlertConsumer implements RocketMQListener<MessageExt> {

     @Override
     public void onMessage(MessageExt message) {
         LogUtil.logReceiverMq(
                 String.format("msgId: %s, body:%s \n", message.getMsgId(), new String(message.getBody())));
     }
 }
`
这个是已知的bug

https://blog.csdn.net/xiaojun081004/article/details/104954802/

https://github.com/apache/rocketmq-spring/pull/188


另外在spring boot rocket消费类中不要去捕获异常

https://blog.csdn.net/xinhuashudiao/article/details/106014635
