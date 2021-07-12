### practise in spring cloud


## 1. spring cloud


1. config center

在spring cloud中，我们会将logback的配置文件的变量放到nacos配置中心中，这里有两点需要注意：
a、springboot读取配置文件是有优先级的，如果使用默认的logback.xml或者logback-spring.xml为配置文件名则会读取不到nacos上的配置。
b、logback中定义变量是使用<springProperty scope="context" name="name" source="spring.application.name"/>这种方式

参考 https://blog.csdn.net/DPnice/article/details/94650402
