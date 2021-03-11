### practise in idea


## 1. 使用idea

使用多年的eclipse 有几个方面导致今年开始换新ide。

1. eclipse最新的版本 min jdk 11. 而我线上机器才是jdk8的，升级上去怕有写代码版本与运行的jdk版本
不一致的情况
2. eclipse （202006rc版）在debug时，调用rpc请求时经常性会卡死，无响应
3. 同team同学与自己开始合作开发java项目时，只要提交，比如有版本冲突，花费解决冲突的时间太就

eclipse是非常优秀的ide，而且它写代码能带来可操控性。

基于上述的几点，开始换idea，本文在记录使用idea过程中出现的问题

## 2. 问题

1. idea出现不能找到类的情况
https://blog.csdn.net/qq_41878811/article/details/86146568
解决方案：
a、maven update
b、clean cache & restart
c、mvn clean install
d、build
e、terminal: maven clean package 看是否ok
f、让idea使用maven来编译 config => build => maven => runner => "delegate ide build/run
  actions to maven"


2. idea 打开一个项目 ，该项目中使用了  mapstruct和 lombok。但发现自动生成的mapper类无法找到属性
只是new targetClass 。然后 return targetClass。
很神奇，后来到 mapstruct的文档
"Lombok - requires having the Lombok classes in a separate module. See for more information
rzwitserloot/lombok#1538 . page 23"
我的sourceClass是基于lombok的@superBuilder注解。而这两个类在同一个module中。
所以就把  builder = @Builder(disableBuilder = true) 掉了。
然后在把idea的缓存全部删除，可以了。所以问题就是：
a、eclipse和idea使用的mapstrucet的plugin都不同。但必须要以mvn clean package为主
b、idea的缓存很严重，很多时候都不知道它干了些什么
c、尽量使用mapstruct的1.3.1 + Lombok 1.18.16的版本，最高版本在跟lombok一起配合使用是有问题
d、两个ide还是有必要的
