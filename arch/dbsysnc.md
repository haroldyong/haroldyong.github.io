### alibaba canal数据同步


## 一. 数据同步


1. 目的

数据同步分全量，增量，如果不需要停在线业务，那么必须选择一个时间点，分别或者同时启用全量和增量同步

canal的官方网站
<a href="https://github.com/alibaba/canal">https://github.com/alibaba/canal</a>


内容xxxxxxxx。

参考 代码
```

# maxmemory-policy allkeys-lru
sh

```

2. kafka 设置

kafka中设置topic的属性


老的命令：

```
./bin/kafka-configs.sh --zookeeper localhost:2181 --alter --entity-name tp_easytong --entity-type topics --add-config retention.bytes=50048576000
```
新命令：
```
./bin/kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-name tp_easytong --entity-type topics --add-config retention.bytes=50048576000
```
参考文档
https://kafka.apache.org/28/documentation.html#topicconfigs
