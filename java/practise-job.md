### practise in job


## 1. 消息产生与发送

一般系统解耦会通过mq消息，在某些情况下消息的到达会比业务系统处理逻辑还快的情况。

### 举个例子

#### a.Order 系统 => 更新 status => 发送消息Order

#### b.业务系统A => 监听消息Order => 处理业务A => 发送业务消息A

#### c.业务系统B => 监听消息A => 处理业务B => 发送业务消息B

#### d.Order 系统 => 监听消息B  => 处理业务


这是个正常的微服务业务逻辑。

这里在设计时有几个问题需要注意:

1. 只要在abcd任何一个环节没有处理到位都会触发消息死循环
2. 如果abcd在业务处理动作和消息发送动作 没有严格的做串行。那么会导致消息到达比业务处理要快


几点好的建议

a. 每个系统发送消息时，尽量在所有业务处理完成后发出。如果不能最好延时10s发出

b. 每个系统在发送消息和处理消息时，最好在注释中带上下游逻辑

c. 凡是设计到写的接口都要添加频次控制,后端并发控制


### spring @Configuration 顺序

1.  业务场景：
    我在启动类中加载hot data（配置文件、经常使用到的数据） 到缓存中，所以定义一个DataStartHelper
    的service，因为是需要使用到DAO层和Cache，而DAO层和Cache是需要提前加载到spring中的，所以报如下错误

    `
    ERROR o.s.b.d.LoggingFailureAnalysisReporter -

   ***************************
   APPLICATION FAILED TO START
   ***************************

   Description:

   The dependencies of some of the beans in the application context form a cycle: dataStartHelper defined in file [xxxxx/DataStartHelper.class]
┌─────┐
|  config defined in class path resource [xxxx/JetCacheConfig.class]
↑     ↓
|  springConfigProvider
    `

代码：


@Component
Class DataStartHelper implements InitializingBean {
{
  ConfigDAO configDao;

  public void init()
  {
    configDao.doSomething();
  }
  @Override
   public void afterPropertiesSet() throws Exception {
       init();
   }
 }

@Repository
class ConfigDAO
{

    @CreateCache(name = " ", cacheType = CacheType.BOTH)
     private Cache<String, List<>> recordCache;

     doSomething()
     {
       recordCache.xxxx
     }
}


现象：

当把   @CreateCache 对象去除掉后，一点问题都没有。
但只要加上jetcache相关代码，必出现上述的问题。


分析原因：

应该是DAO中的dosomething使用到 recordCache的方法，而这个时候 recordCache是依赖于外部的 cacheConfig来生成的。所以这里会出现异常。从而报上述的错误。


解决方法：

DataStartHelper为标准的service组建


@DependsOn({"springConfigProvider"})
@AutoConfigureAfter(JetCacheConfig.class)
@Configuration
public class DataStartConfig implements InitializingBean {

    @Autowired
    DataStartHelper dataStartHelper;


    @Override
    public void afterPropertiesSet() throws Exception {
        dataStartHelper.init();

    }

}

可以解决。最重要的是 @DependsOn({"springConfigProvider"})


当使用一个开源框架时，一定要对其中参数了解清楚，比如jetcache设置缓存过期时间有 expireAfterWrite
expireAfterAccess 这些，有StatIntevalMins 这些参数还是需要了解清楚后才可以使用。否则会造成线上故障

业务场景：

外部搜索引擎洗数据,one by one 读取订单系统，然后调用订单系统的rpc去获取全部业务模型数据，这时订单系统是要去缓存这些数据的，这时会对缓存系统造成很大的内存压力，如果订单系统没有设置缓存过期时间，那么整个内存会打爆。






## 2.Mysql 数据库时间索引问题

我有一张表具体字段就不列了。

CREATE TABLE `order_main` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '缺省字段,物理主键',
  .
  .
  .
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '缺省字段,创建时间',
  `gmt_modify` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '缺省字段,修改时间',
    PRIMARY KEY (`id`),
    KEY `idx_gmt_modify` (`gmt_modify`)
) ENGINE=InnoDB AUTO_INCREMENT=229872 DEFAULT CHARSET=utf8mb4 ROW_FORMAT=DYNAMIC


然后有个查询语句

SELECT * FROM order_main use index(idx_gmt_modify)
  WHERE    gmt_modify >= "2020-12-12 16:59:00"
        AND   gmt_modify <= "2020-12-21 12:59:00" AND grd_delete = 0
        ORDER BY gmt_create DESC LIMIT 192650,50 ;


这时它的explain如下





## 3.升级mac os => mac os bigsur 11.1 引起的问题


之前是mac 10.3什么版本来着，心血来潮，升级到11.1， 这时eclipse启动不了，出现

Failed to create the Java Virtual Machine.

错误

解决步骤：

1. 首先将本地的jdk8 版本升级到 最新的 1.8.0_271
