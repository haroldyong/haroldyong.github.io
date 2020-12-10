## dubbo无法获取apollo配置

最近在用 springboot + dubbo + apollo搭建模块时碰到一个问题

### 问题描述
`
actory method 'referenceConfig' threw exception; nested exception is java.lang.IllegalStateException: No application config found or it's not a valid config! Please add <dubbo:application name="..." /> to your spring config.
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185)
        at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:650)
        ... 90 common frames omitted
Caused by: java.lang.IllegalStateException: No application config found or it's not a valid config! Please add <dubbo:application name="..." /> to your spring config.
        at org.apache.dubbo.config.AbstractInterfaceConfig.checkApplication(AbstractInterfaceConfig.java:216)
        at org.apache.dubbo.config.ReferenceConfig.checkAndUpdateSubConfigs(ReferenceConfig.java:239)
        at org.apache.dubbo.config.ReferenceConfig.get(ReferenceConfig.java:244)
        .........

`

### 原因分析
