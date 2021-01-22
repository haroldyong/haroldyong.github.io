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
