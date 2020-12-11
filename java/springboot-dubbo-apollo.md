## dubbo无法获取apollo配置

最近在用 springboot + dubbo + apollo搭建模块时碰到一个问题

### 问题描述
`
[main] ERROR o.s.boot.SpringApplication - Application run failed
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'referenceConfig' defined in class path resource [xxx.xxxx.middleware/DubboConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [xxx.xxx.client.CasRpc]: Factory method 'referenceConfig' threw exception; nested exception is java.lang.IllegalStateException: No application config found or it's not a valid config! Please add <dubbo:application name="..." /> to your spring config.
        at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:655)
        at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:483)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(Abstr
actAutowireCapableBeanFactory.java:1336)
       at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowire
CapableBeanFactory.java:1176)
       at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapabl
eBeanFactory.java:556)
       at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableB
eanFactory.java:516)
       at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:324)
       at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry
.java:234)
       at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:322)
       at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202)
       at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBea
nFactory.java:897)
       at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicati
onContext.java:879)
       at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:551)
       at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicat
ionContext.java:141)
       at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:747)
       at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:397)
       at org.springframework.boot.SpringApplication.run(SpringApplication.java:315)
       at org.springframework.boot.SpringApplication.run(SpringApplication.java:1226)
       at org.springframework.boot.SpringApplication.run(SpringApplication.java:1215)
       at com.bluewhale.message.server.Application.main(Application.java:21)
       Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [xxx.xxx.client.CasRpc]: Factory method 'referenceConfig' threw exception; nested exception is java.lang.IllegalStateException: No application config found or it's not a valid config! Please add <dubbo:application name="..." /> to your spring config.
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185)
        at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:650)
        ... 19 common frames omitted
Caused by: java.lang.IllegalStateException: No application config found or it's not a valid config! Please add <dubbo:application name="..." /> to your spring config.
        at org.apache.dubbo.config.AbstractInterfaceConfig.checkApplication(AbstractInterfaceConfig.java:216)
        at org.apache.dubbo.config.ReferenceConfig.checkAndUpdateSubConfigs(ReferenceConfig.java:239)
        at org.apache.dubbo.config.ReferenceConfig.get(ReferenceConfig.java:244)
        .........

`

### 原因分析
我的程序启动过程，首先是在spring interceptor中加载一个拦截器，这个拦截器中引用一个dubbo consume
class。所以有 dubbo config 类中加载这个class。

分析这个原因应该是dubbo config类启动是没有读取到配置信息。为分析这个过程，我做了几个动作
1. 将dubbo的配置类 和 intercepor注释掉。可以成功启动，也可以读取到apollo中关于dubbo的配置（ps：spring boot是按照约定大于配置原则来的，所以如果配置了 dubbo.application.name 则是可以读取到的）
2. 改名dubbo.application.name 改为 application.name 则读取不到
程序启动后还是会出现上面的问题。网上搜索发现这篇文章。
<a href="https://xobo.org/spring-boot-apollo-dubbo-xml-error/">Spring Boot接入 apollo 后启动 dubbo 报错</a>
解决的核心 "1.2.0版本已经发布，支持把Apollo配置加载提到初始化日志系统之前，可以解决此issue中提到的问题
" 具体落实到配置就是 apollo.bootstrap.eagerLoad.enabled=true


### dubbo spring 代码

org.apache.dubbo.spring.boot.context.event.OverrideDubboConfigApplicationListener

代码中

line 61:

if (override) {

          SortedMap<String, Object> dubboProperties = filterDubboProperties(environment);

          ConfigUtils.getProperties().putAll(dubboProperties);

          if (logger.isInfoEnabled()) {
              logger.info("Dubbo Config was overridden by externalized configuration {}", dubboProperties);
          }
      } else {
          if (logger.isInfoEnabled()) {
              logger.info("Disable override Dubbo Config caused by property {} = {}", OVERRIDE_CONFIG_FULL_PROPERTY_NAME, override);
          }
      }


方法 filterDubboProperties

public static SortedMap<String, Object> filterDubboProperties(ConfigurableEnvironment environment) {

      SortedMap<String, Object> dubboProperties = new TreeMap<>();

      Map<String, Object> properties = EnvironmentUtils.extractProperties(environment);

      for (Map.Entry<String, Object> entry : properties.entrySet()) {
          String propertyName = entry.getKey();

          if (propertyName.startsWith(DUBBO_PREFIX + PROPERTY_NAME_SEPARATOR)
                  && entry.getValue() != null) {
              dubboProperties.put(propertyName, entry.getValue().toString());
          }

      }

      /**
        * The prefix of property name of Dubbo
        */
       public static final String DUBBO_PREFIX = "dubbo";


所以到这里就很清晰的明白，所有dubbo配置一定要以 dubbo作为前缀


### 补充

整合springboot 和线程池
https://www.jianshu.com/p/3d875dd9d5db

over      
