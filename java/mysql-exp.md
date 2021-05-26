### practise in mysql


## 1. mysql


1. 客户端问题

之前mac上安装的mysql版本是5.7.15 ?，后来升级到5.7.28，然后使用命令行 mysql -h x.x.x.x -uroot -p 登录到远端mysql服务器时出现
"unknow error #table_open_cache = 1000" 这种错误,主要原因是因为从5.7.15升级到。5.7.28时那个参数 table_open_cache = 1000 在my.cnf中找不到，所以直接在my.cnf中注视掉这行代码就可以了。
