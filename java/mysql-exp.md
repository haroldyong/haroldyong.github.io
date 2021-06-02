### practise in mysql


## 1. mysql


1. 客户端问题

之前mac上安装的mysql版本是5.7.15 ?，后来升级到5.7.28，然后使用命令行 mysql -h x.x.x.x -uroot -p 登录到远端mysql服务器时出现
"unknow error #table_open_cache = 1000" 这种错误,主要原因是因为从5.7.15升级到。5.7.28时那个参数 table_open_cache = 1000 在my.cnf中找不到，所以直接在my.cnf中注视掉这行代码就可以了。


2. konga 是kong的admin的客户端，网上的各种安装方法有个问题
konga支持mysql的连接url
node ./bin/konga.js  prepare --adapter mysql --uri mysql://konga:konga@localhost:3306/konga

这个uri设置一定要弄清楚

先安装kong，然后安装konga，konga支持多种数据库，但PostgresSQL对版本有要求。所以我选择mysql

kong只支持PostgresSQL 和 cassandra 两种数据库
