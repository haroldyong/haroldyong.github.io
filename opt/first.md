### Tips of Visual Studio Code


## 一. Markdown


1. How to preview in vsc

Visual Studio Code 原生就支持高亮Markdown的语法，想要一边编辑一遍预览，有两种方法：

* `Ctrl + Shift + P `调出主命令框，输入 Markdown，应该会匹配到几项 Markdown相关命令

* 先按 `Ctrl + K` ，然后放掉，紧接着再按` v `，也能调出实时预览框

2. Markdown 语法参考

<a href="https://www.runoob.com/markdown/md-tutorial.html">https://www.runoob.com/markdown/md-tutorial.html</a>



3. 如何在 vsc中使用 PlantUML 画图

PlantUML 的官方网站 <a href="https://plantuml.com/zh/">https://plantuml.com/zh/</a> 

安装 PlantUML 插件, 新建文件后缀名为wsd , <a href="./testPreview.wsd">testPreview.wsd</a> ，然后按照它的语法写

```

@startuml

title new page test

Alice -> Bob : message 1

Bob -> Bob : message 2

Alice -> Bob : message 3

Bob -> Bob : message 4

@enduml

```
使用plantuml的预览功能就可以查看，如何切换，使用vsc的快捷方式 `Ctrl + Shift + P `调出主命令框，输入 PlantUml，调用preview相关命令

<a href="./plant.png">
