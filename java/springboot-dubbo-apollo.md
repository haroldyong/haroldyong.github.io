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
