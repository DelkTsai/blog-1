
+++
date        = "2016-09-07T09:07:06+08:00"
title       = "Dubbo-入门示例"
description = "Dubbo-入门示例"
weight = 94
+++

这篇文章是我在学习使用Dubbo搭建的一个入门示例，在查看该示例之前，你应该先从整体上了解一下 Dubbo .

- [Dubbo 官网](http://dubbo.io/) 
- [Dubbo 介绍](https://github.com/alibaba/dubbo/wiki/user-guide-intro)
- [官方的 Quick start 文档](https://github.com/alibaba/dubbo/wiki/user-guide-quick-start)


如果你已经阅读完，Dubbo 介绍，那么你应该知道 “注册中心” ， “服务提供者” , “服务消费者” 这三个概念。 那么我们就先从这三者开始

### 注册中心

Dubbo 官方推荐使用 zookeeper 注册中心用于线上生产环境，那么我们先从安装zookeeper开始。

- [zookeeper 是什么？](http://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/)



#### Zookeeper注册中心安装

> 参考 [Dubbo 管理员指南 ](https://github.com/alibaba/dubbo/wiki/admin-guide-install-manual)

* 安装:

```shell
wget http://www.apache.org/dist//zookeeper/zookeeper-3.3.3/zookeeper-3.3.3.tar.gz
tar zxvf zookeeper-3.3.3.tar.gz
cd zookeeper-3.3.3
cp conf/zoo_sample.cfg conf/zoo.cfg
```

* 配置:

```shell
vi conf/zoo.cfg
```

如果不需要集群，zoo.cfg的内容如下：(其中data目录需改成你真实输出目录)
> zoo.cfg

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/dubbo/zookeeper-3.3.3/data
clientPort=2181
```

如果需要集群，zoo.cfg的内容如下：(其中data目录和server地址需改成你真实部署机器的信息)
> zoo.cfg

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/dubbo/zookeeper-3.3.3/data
clientPort=2181
server.1=10.20.153.10:2555:3555
server.2=10.20.153.11:2555:3555
```

并在data目录下放置myid文件：(上面zoo.cfg中的dataDir)

```shell
mkdir data
vi myid
```

myid指明自己的id，对应上面zoo.cfg中server.后的数字，第一台的内容为1，第二台的内容为2，内容如下：
> myid

```
1
```

* 启动:

```shell
./bin/zkServer.sh start
```

* 停止:

```shell
./bin/zkServer.sh stop
```

* 命令行: (See: http://zookeeper.apache.org/doc/r3.3.3/zookeeperAdmin.html)

```shell
telnet 127.0.0.1 2181
dump
```

### 服务提供者 provider

###### 使用 maven 构建一个 Quick start 项目 , 起名为 provider 

命令：

```
mvn archetype:generate -DgroupId=com.izhengyin.dubbo -DartifactId=provider -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

###### 修改 pom.xml 如下:


```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.izhengyin.dubbo</groupId>
  <artifactId>provider</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version> 
  <name>provider</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.alibaba/dubbo -->
	<dependency>
	    <groupId>com.alibaba</groupId>
	    <artifactId>dubbo</artifactId>
	    <version>2.5.3</version>
	</dependency>
	<!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
	<dependency>
	    <groupId>com.101tec</groupId>
	    <artifactId>zkclient</artifactId>
	    <version>0.2</version>
	</dependency>
	
    
  </dependencies>
</project>

```

###### 新建一个 provider.xml 的提供最配置文件

Path: provider/src/main/resources/META-INF/spring/provider.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://code.alibabatech.com/schema/dubbo
        http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="hello-world-app"  />

    <!-- 使用zookeeper广播注册中心暴露服务地址 -->
    <dubbo:registry address="zookeeper://127.0.0.1:2181" />

    <!-- 用dubbo协议在20880端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880" />
    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="com.izhengyin.dubbo.DemoService" ref="demoService" />
    <!-- 和本地bean一样实现服务 -->
    <bean id="demoService" class="com.izhengyin.dubbo.DemoServiceImpl" />

</beans>
```

###### 新建 DmeoService 接口内容如下：

```
package com.izhengyin.dubbo;

public interface DemoService {
	String hello(String name);
}
```

###### 新建  DmeoServiceImpl 实现 DmeoService 接口内容如下：

```
package com.izhengyin.dubbo;

public class DemoServiceImpl implements DemoService {

	public String hello(String name) {
		return "Hello "+name;
	}

}
```

###### 修改 App 的 main 方法用于启动 provider

```
package com.izhengyin.dubbo;

import java.io.IOException;

import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args ) throws IOException
    {
    	
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("./META-INF/spring/provider.xml");
        
        context.start();
        
        
        System.out.println("Started");
        
        System.in.read();
    }
}

```

###### 启动provider

```
 mvn exec:java -Dexec.mainClass="com.izhengyin.dubbo.App" -Dexec.cleanupDaemonThreads=false
```

如果运行正常，你将看到类似如下输出：



```
root@vm-105:/data/smb/dubbo2/provider# mvn exec:java -Dexec.mainClass="com.izhengyin.dubbo.App" -Dexec.cleanupDaemonThreads=false
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building provider 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- exec-maven-plugin:1.5.0:java (default-cli) @ provider ---
log4j:WARN No appenders could be found for logger (org.springframework.context.support.ClassPathXmlApplicationContext).
log4j:WARN Please initialize the log4j system properly.
Started


```

> 注：此时服务将被挂起，按任意键后服务退出


### 服务的消费者 consumer 

使用 maven 构建一个 Quick start 项目 , 起名为 consumer 

命令：

```
mvn archetype:generate -DgroupId=com.izhengyin.dubbo -DartifactId=ocnsumer -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

修改 pom.xml 如下:


```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.izhengyin.dubbo</groupId>
  <artifactId>provider</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version> 
  <name>provider</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.alibaba/dubbo -->
	<dependency>
	    <groupId>com.alibaba</groupId>
	    <artifactId>dubbo</artifactId>
	    <version>2.5.3</version>
	</dependency>
	<!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
	<dependency>
	    <groupId>com.101tec</groupId>
	    <artifactId>zkclient</artifactId>
	    <version>0.2</version>
	</dependency>
	
    
  </dependencies>
</project>

```

新建一个 consumer.xml 的提供最配置文件

Path: consumer/src/main/resources/META-INF/spring/consumer.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans       
	 http://www.springframework.org/schema/beans/spring-beans.xsd        
	 http://code.alibabatech.com/schema/dubbo        
	 http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-of-helloworld-app"  />

    <!-- 使用multicast广播注册中心暴露发现服务地址 -->
    <dubbo:registry address="zookeeper:/127.0.0.1:2181" />

    <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
    <dubbo:reference id="demoService" interface="com.izhengyin.dubbo.DemoService" />
</beans>

```

新建 DmeoService 接口内容如下：

```
package com.izhengyin.dubbo;

public interface DemoService {
	String hello(String name);
}
```

修改 App 的 main 方法用于启动 consumer

```
package com.izhengyin.dubbo;

import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args )
    {
    	 	 ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("./META-INF/spring/consumer.xml");
         context.start();
         DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
         String hello = demoService.hello("world"); // 执行远程方法
         System.out.println( hello ); // 显示调用结果
         
    }
}
```

###### 启动 consumer

```
 mvn exec:java -Dexec.mainClass="com.izhengyin.dubbo.App" -Dexec.cleanupDaemonThreads=false
```

如果运行正常，你将看到类似如下输出：

```
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building consumer 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- exec-maven-plugin:1.5.0:java (default-cli) @ consumer ---
log4j:WARN No appenders could be found for logger (org.springframework.context.support.ClassPathXmlApplicationContext).
log4j:WARN Please initialize the log4j system properly.
Hello world
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 4.113 s
[INFO] Finished at: 2016-06-29T11:12:04+08:00
[INFO] Final Memory: 15M/152M
[INFO] ------------------------------------------------------------------------
```