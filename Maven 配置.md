# Maven 配置

## 本地仓库路径

在[maven目录]/conf/setting.xml中搜索`localRepository`

```xml
<localRepository>/home/sure/模板/maven/repo</localRepository>
```

中间的是想要放置的路径

## 配置中心仓库（远程仓库）

还是setting.xml，搜索mirrors

maven里默认应该会有一个`mirrors`标签，在mirrors标签里添加 `<mirror>` 标签

```xml
<mirrors>
    <mirror>
        <id>alimaven</id>
        <mirrorOf>*</mirrorOf>	(*可以换成central)
        <name>aliyun maven repo</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirrors>
```
 ## IDEA配置自己的maven

IDEA-->设置-->Build,Execut.....-->Build Tools --> Maven

设置 `Maven home directory` 为自己的maven目录

设置 `User setting file` 为自己maven的conf目录下的setting.xml

设置 `Local repository` 为刚才配置的本地仓库地址

**ps：**有时使用maven创建项目时会很慢，可以在IDEA的maven设置 --> Runner --> VM Options

添加 

```
-DarchetypeCatalog=local
```

![1556877290486](C:\Users\I\AppData\Roaming\Typora\typora-user-images\1556877290486.png)