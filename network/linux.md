### practise in linux


## 1.


1. centos7 自启动程序

在centos7 中，有些基础中间件是需要自启动的，比如zk ，nacos等
需要在/etc/systemd/system/ 中添加 zookeeper.service

```
[Unit]
Description=zookeeper
After=network.target,local-fs.target

[Service]
Type=forking
ExecStart=/data/webserver/zookeeper/bin/zkServer.sh start
ExecReload=/data/webserver/zookeeper/bin/zkServer.sh restart
ExecStop=/data/webserver/zookeeper/bin/zkServer.sh stop
PrivateTmp=true


[Install]
WantedBy=default.target

```
然后使用systemctl start zookeeper启动
systemctl enable zookeeper加添自启动里面

这里一般会有个问题，会找不到java_home的错误，如果找不到，直接在启动脚步里面设置一下 export java_home=''
