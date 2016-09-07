
+++
date        = "2016-09-07T09:07:06+08:00"
title       = "Maven 使用笔记"
description = "Maven 使用"
weight = 95
+++


### Maven命令

- 创建一个简单的Java工程

```
mvn archetype:generate -DgroupId=com.izhengyin.maven -DartifactId=quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

- 创建一个java的web工程
```
mvn archetype:generate -DgroupId=com.izhengyin.maven -DartifactId=quickstartWebApp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

- 打包
    - mvn package
    - 运行jar java -cp target/quickstart-1.0-SNAPSHOT.jar com.izhengyin.maven.App 
- 编译
    - mvn compile
- 编译测试程序
    - mvn test-compile
- 清空
    - mvn clean
- 运行测试
    - mvn test
- 生成站点目录:
    - mvn site
- 生成站点目录并发布
    - mvn site-deploy
- 安装当前工程的输出文件到本地仓库:
    - mvn install
- 安装指定文件到本地仓库
```
mvn install:install-file -DgroupId=<groupId> -DartifactId=<artifactId> -Dversion=1.0.0 -Dpackaging=jar -Dfile=<myfile.jar>
```
- 查看实际pom信息:
    - mvn help:effective-pom
- 分析项目的依赖信息
    - mvn dependency:analyze 或 mvn dependency:tree
- 跳过测试运行maven任务
    - mvn -Dmaven.test.skip=true XXX
- 生成eclipse项目文件:
    - mvn eclipse:eclipse
- 查看帮助信息
    - mvn help:help 或 mvn help:help -Ddetail=true
- 查看插件的帮助信息
    - mvn <plug-in>:help，比如：mvn dependency:help 或 mvn ant:help 等等。
- 运行某个类的主方法
```
mvn exec:java -Dexec.mainClass="com.izhengyin.App" -Dexec.args="Hello maven !"
public class App
{
    public static void main( String[] args )
    {
        System.out.println("----------------");
        System.out.println( args[0]+" "+args[1]+args[2]);
        System.out.println("----------------");
    }
}

- mvn exec:help -Ddetail=true -Dgoal=java  获取其他参数

```

### Maven依赖

- 依赖范围
    - compile 编译范围 【需要立即编译的】
    - provided 已提供范围【比如已打包到的jar】
    - runtime 运行时范围 【运行时编译的】
    - test 测试范围 【测试时编译的】
    - system 系统范围【引入本地jar包】
- 依赖配置
```
<dependency>  
    <groupId>org.springframework</groupId>  
    <artifactId>spring-core</artifactId>  
    <version>${springframework.version}</version>  
    <type>jar</type>  
    <scope>compile</scope>  
</dependency>
```
- 字段说明
    - groupId,必选，实际隶属项目
    - artifactId,必选，其中的模块
    - version必选，版本号
    - type可选，依赖类型，默认jar
    - scope可选，依赖范围，默认compile
    - optional可选，标记依赖是否可选，默认false
    - exclusion可选，排除传递依赖性，默认空

- 本地仓库构建
```
mvn install:install-file -Dfile=/mnt/java/quickstart/src/lib/netty-all-4.1.0.CR7.jar -DgroupId=io.netty -DartifactId=netty -Dversion=4.1.0.CR7 -Dpackaging=jar
```


### 参考
http://www.cnblogs.com/JeffreySun/archive/2013/03/14/2960573.html