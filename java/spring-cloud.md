### practise in spring cloud


## 1. spring cloud


1. config center

在spring cloud中，我们会将logback的配置文件的变量放到nacos配置中心中，这里有两点需要注意：
a、springboot读取配置文件是有优先级的，如果使用默认的logback.xml或者logback-spring.xml为配置文件名则会读取不到nacos上的配置。
b、logback中定义变量是使用<springProperty scope="context" name="name" source="spring.application.name"/>这种方式

参考 https://blog.csdn.net/DPnice/article/details/94650402

2. 升级spring cloud alibaba 2021.0.1.0 碰到的问题

2022-6-14, 升级组件后,使用官方的sample （spring-cloud-alibaba-dubbo-provider 。 https://github.com/alibaba/spring-cloud-alibaba/tree/2.2.x/spring-cloud-alibaba-examples/spring-cloud-alibaba-dubbo-examples/spring-cloud-dubbo-provider-web-sample）
运行后出现下面错误信息

```

***************************
APPLICATION FAILED TO START
***************************

Description:

The dependencies of some of the beans in the application context form a cycle:

   targeterBeanPostProcessor defined in class path resource [com/alibaba/cloud/dubbo/autoconfigure/DubboOpenFeignAutoConfiguration.class]
      ↓
   com.alibaba.cloud.dubbo.metadata.repository.DubboServiceMetadataRepository (field private com.alibaba.cloud.dubbo.service.DubboMetadataServiceProxy com.alibaba.cloud.dubbo.metadata.repository.DubboServiceMetadataRepository.dubboMetadataConfigServiceProxy)
      ↓
   com.alibaba.cloud.dubbo.service.DubboMetadataServiceProxy
┌─────┐
|  com.alibaba.cloud.dubbo.autoconfigure.DubboMetadataAutoConfiguration (field private com.alibaba.cloud.dubbo.metadata.resolver.MetadataResolver com.alibaba.cloud.dubbo.autoconfigure.DubboMetadataAutoConfiguration.metadataResolver)
└─────┘


Action:

Relying upon circular references is discouraged and they are prohibited by default. Update your application to remove the dependency cycle between beans. As a last resort, it may be possible to break the cycle automatically by setting spring.main.allow-circular-references to true.


```

通过分析应该是spring boot 2.6之后默认不支持循环依赖，所以这个错误。查到官方的wiki，也有开发者碰到我同样的问题（https://github.com/alibaba/spring-cloud-alibaba/issues/2475）。
根据sca(spring cloud alibaba) 的官方答复，spring cloud dubbo组件将从 sca中移除，由dubbo boot单独实现 。(https://github.com/alibaba/spring-cloud-alibaba/issues/2398)
```
由于Spring Cloud Dubbo组件社区做了一些调研发现真正深度使用的用户不多，另外该模块设计本身存在一些问题成熟度不够，复杂度很高，长期缺乏维护答疑同学，因此为了降低社区维护成本，社区第4次双周会讨论觉得将Spring Cloud Dubbo从社区主干分支中移除可能更适合社区当前发展规划.

如果希望实现 Spring Cloud 应用调用 Dubbo 服务，建议先直接通过 Spring Boot 将 Dubbo 服务暴露为 RESTful API 的方式以供对外调用。这样两个组件互相都是独立的出现问题也方便排查，比较推荐使用。


如果你要在一个应用里面使用Dubbo直接用引入dubbo项目原生的依赖吧，不要再使用spring-cloud-starter-dubbo依赖了
```

所以我的结论：Spring-cloud其实是spring社区定义的一套微服务规范，比如服务发现，网关入口，微服务调用，配置中心，限流分流，负载均衡，链路监控等等。然后各大厂商纷纷跟进这个规范，实现它，以便融入Spring社区。
但从Alibaba的Dubbo设计初心来分析，dubbo当初是定义为rpc调用框架，所以它的优势在于微服务之间的短平快的调用，所以其实它跟Spring cloud的 feign的思路是不一致的，
一个走rpc调用，一个走http 调用；一个追求高性能&大并发，一个追求标准化&无状态化。而SCA这个项目就是基于融入spring社区的目的而通过改造dubbo去实现这些规范。本身它跟进Dubbo项目组就是两拨人，后期发现改造的过程中有很多问题，所以就从SCA中把SCD剔除出来，所以从这个角度分析，我认为SCA不是阿里的主要方向。
那么我们怎么追随阿里的技术步骤了？我的判断是 dubbo + spring boot 这种搭积木的方式。可以通过各个社区来整合资源，在spring cloud 的指导思想下，通过使用各个组件自我组装，自我发现，当然必然包括自我踩坑去实现业务场景。

3. 将 logback.xml 文件放到 nacos上，dataId必须是xml文件，必须以xml文件结尾


```
logging:
  config: http://${spring.cloud.nacos.server-addr}/nacos/v1/cs/configs?username=${spring.cloud.nacos.username}&password=${spring.cloud.nacos.password}&group=${spring.cloud.nacos.config.group}&tenant=${spring.cloud.nacos.config.namespace}&dataId=logback-config.xml
  level:
    com.alibaba.nacos: warn

```

logback-config.xml 必须是url的最后


4. sentinel 是流控框架，可以通过nacos加载规则的。做到持久化

sentinel官方有一个 dashboard的应用程序可以理解为sentinel的server端。
所有的sentinel client应用会在本地日志中记录资源方位的秒级数据。
sentinel client 会本地启动一个netty api service，sentinel dashboard server会通过定时调用http get请求从client中拿 metric数据

sentinel 上生产环境，还是要好好研究下
