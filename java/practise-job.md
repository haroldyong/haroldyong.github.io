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
2. 设置 java_home
3. 修改eclipse的配置
to /Applications/Eclipse.app/Contents/Info.plist

<string>-vm</string><string>/Library/Java/JavaVirtualMachines/jdk1.8.0_271.jdk/Contents/Home/bin/java</string>


参考帖子

https://stackoverflow.com/questions/62647625/not-able-to-run-eclipse-on-macos-big-sur



## 4.分层概念

一般应用分 web  / server  /manager - adapter || dao

我们抛出异常建议在server中，下层一定要尽量捕捉到所有的异常，保证上层调用有值



## 5.MAC系统防火墙使用

有些时候我们需要模拟中间件挂掉的场景，这个时候需要让web服务器与中间件(redis mysql )断开
这个时候可以使用mac 的pf

/etc/pf.conf

```
scrub-anchor "com.apple/*"
nat-anchor "com.apple/*"
nat-anchor "custom"
rdr-anchor "com.apple/*"
rdr-anchor "custom"
dummynet-anchor "com.apple/*"
anchor "com.apple/*"
anchor "custom"
load anchor "com.apple" from "/etc/pf.anchors/com.apple"
load anchor "custom" from "/etc/pf.anchors/custom"

```

然后建立文件/etc/pf.anchors/custom

```
table <blockedips> persist file "/Users/Shared/data/server/config/black_ip_lists"
block in log proto {tcp,udp} from <blockedips> to any

```
然后在文件/Users/Shared/data/server/config/black_ip_lists 中填写需要切断的ip地址

使用如下命令

```
清除所有已经建立的规则，并重新读取配置文件
pfctl -F all -f /etc/pf.conf

```

参考：https://blog.csdn.net/shuishen49/article/details/77587527
https://blog.csdn.net/huithe/article/details/7323146


## 6.vpn搭建

vpn搭建
https://github.com/hwdsl2/docker-ipsec-vpn-server


## 7.在mac 11.1根目录下建软链

修改这个文件
sudo vim /etc/synthetic.conf

添加你需要软链的目录 add :

data	/xxx/data

https://blog.csdn.net/chinamen1/article/details/109760125

重启ok


## 8. mac自启动服务配置

如果你使用Macports等包管理器安装时，会自动帮你写入一个plist文件到/Library/LaunchDaemons/xxxxxx.plist，随后执行sudo port load redis类似这样的命令即可启动，并开机自动启动。
在Mac里有一个命令行工具叫做：launchctl，可以用来控制服务的自动启动或者关闭。一般的语法是
sudo launchctl load /path/to/service.plistsudo launchctl unload /path/to/service.plist
一般plist文件放在这三个地方：

/Library/LaunchDaemons/
/Library/LaunchAgents/
~/Library/LaunchAgents/
具体plist文件的写法可以自行参考已有的文件。在安装Redis、MongoDB等开发工具时推荐使用Macports，以便日后的维护管理。Brew这个我个人不是很推荐。

你可以写一个plist文件放到~/Library/Launch Agents/下面，文件里描述你的程序路径和启动参数，那么这个用户登录时就会启动这个程序了，而且是杀不了的哦

被杀了之后会自动重新启动
如果需要把它停止的话，运行一下命令
launchctl unload ~/Library/Launch Agents/com.your company.porduct
如果放到/Library/Launch Agents/下面的话，就是一开机就启动哦～
