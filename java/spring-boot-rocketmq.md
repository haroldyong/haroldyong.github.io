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


## 2. spring-boot 使用 pagehelper时碰到问题

我的项目是spring-boot + DD ElasticSob + pagehelper分页查询

最初是在spring session factory 中手工写

```

@Bean
 public PageInterceptor pageInterceptor() {
     PageInterceptor interceptor = new PageInterceptor();
     Properties properties = new Properties();
     properties.setProperty("helperDialect", "mysql");
     properties.setProperty("reasonable", "false");
     properties.setProperty("supportMethodsArguments", "true");
     properties.setProperty("offsetAsPageNum", "true");
     interceptor.setProperties(properties);
     return interceptor;
 }

 ```

 在idea中跑单测ok，本地启动跑也是ok的。但是部署到服务器后发现只要有分页查询必然会报告

 ```

 Caused by: java.lang.RuntimeException: 在系统中发现了多个分页插件，请检查系统配置!
         at com.github.pagehelper.PageHelper.skip(PageHelper.java:59)
         at com.github.pagehelper.PageInterceptor.intercept(PageInterceptor.java:96)
         at org.apache.ibatis.plugin.Plugin.invoke(Plugin.java:61)

 ```

 后将手工配置删除，在spring.yam中添加配置，可以完美解决 over

 https://www.bbsmax.com/A/l1dyWBBAJe/
