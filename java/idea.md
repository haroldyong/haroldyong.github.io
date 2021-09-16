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

3. 最近升级idea到2020.3版本，当build 包含mapstruct项目时报

```
/Users/Shared/data/work/whale/src_lib/pangu/src/bw-message/message-core/src/main/java/com/bluewhale/message/core/converter/CoreBizBeanMapper.java:67:8
java: Internal error in the mapping processor: java.lang.NullPointerException   at org.mapstruct.ap.internal.processor.DefaultVersionInformation.createManifestUrl(DefaultVersionInformation.java:182)      at org.mapstruct.ap.internal.processor.DefaultVersionInformation.openManifest(DefaultVersionInformation.java:153)   at org.mapstruct.ap.internal.processor.DefaultVersionInformation.getLibraryName(DefaultVersionInformation.java:129)     at org.mapstruct.ap.internal.processor.DefaultVersionInformation.getCompiler(DefaultVersionInformation.java:122)    at

```

这种错误，后来添加 -Djps.track.ap.dependencies=false =>
Preferences | Build, Execution, Deployment | Compiler , 在dialog上面 build process vm options

[id]: https://stackoverflow.com/questions/65112406/intellij-idea-mapstruct-java-internal-error-in-the-mapping-processor-java-lang "解决方法"


4. 使用已经项目创建maven脚手架时，碰到的问题，记录一下。

步骤1: 确保项目的根pom.xml 有下面信息
```
<build>
        <finalName>zy-cloud</finalName>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-archetype-plugin</artifactId>
                    <version>3.2.0</version>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                        <encoding>UTF-8</encoding>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>3.2.0</version>
                    <configuration>
                        <encoding>UTF-8</encoding>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
```

步骤2: 使用在项目的根pom下使用命令 mvn archetype:create-from-project

这时会出现 " Invoker process ended with result different than 0! " 类似这样的错误

其实是因为有些maven插件是使用默认的配置 {user}/.m2/settings.xml ；如果当前目录不存在这个配置
则会出错

5. 使用阿里迁移私服jar到公共仓库的工具。
https://help.aliyun.com/document_detail/75170.html?spm=5176.10695662.1996646101.searchclickresult.5c96731bYubpvZ


 java -jar /Users/harold/Downloads/migrate-local-repo-tool.jar -cd "/Users/harold/resp/" -t "http://maven.hzlinks.net/repository/3rd-party/" -u admin -p hzsun@310012

 注意这个 /Users/harold/resp/ 这个目录是整个仓库的根目录

 6. 改造原始jar文件

 先使用rar之类的解压出目录，添加需要的类

 然后再变为jar, 命令如下:
 jar cvfm mybatis-plus-generator-3.4.3.3.jar ./mybatis-plus-generator-3.4.3.3/META-INF/MANIFEST.MF -C mybatis-plus-generator-3.4.3.3/ .
