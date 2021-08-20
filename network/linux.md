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



2. 另外还有一种方法，在 /usr/lib/systemd/system 中添加 rmq_broker.service，内容入下:

```

[Unit]
Description=rmq
After=network.target

[Service]
Type=simple
Environment=JAVA_HOME=/opt/java/jdk1.8.0_291
ExecStart=/usr/local/rocketmq/bin/mqbroker -c /usr/local/rocketmq/conf/broker.conf
ExecStop=/usr/local/rocketmq/bin/mqshutdown broker

[Install]
WantedBy=multi-user.target

```

nginx的安装配置在路径 /etc/systemd/system/nginx.service

```
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
[Install]
WantedBy=multi-user.target

```
然后使用systemctl start nginx.service 启动
systemctl enable nginx.service 加添自启动里面


3. 阿里云ecs中文问题

阿里云ecs默认是支持bash 中文显示，比如安装yum 时错误，会有中文提示，但在 显示中文的文件时是有乱码，
使用下面命令可以解决这个问题

```
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8

```

可以在/etc/profile 文件最后添加上面两句话，永久性解决问题
