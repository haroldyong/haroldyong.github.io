### practise in tomcat


## 1. 问题


1. tcp6的问题

centos启动tomcat后局域网无法访问，发现8080端口被tcp6占用解决方法

在/etc下的sysctl.conf文件末尾添加如下：

net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1

然后# reboot，在重新启动tomcat

查看防火状态
systemctl status firewalld
暂时关闭防火墙
systemctl stop firewalld
永久关闭防火墙
systemctl disable firewalld
重启防火墙
systemctl enable firewalld
