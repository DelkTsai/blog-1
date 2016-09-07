

+++
date        = "2016-09-07T09:07:06+08:00"
title       = "Log4j2 - 简单使用"
description = "Log4j2 - 简单使用"
weight = 93
+++

#### 配置maven

###### 参考: http://logging.apache.org/log4j/2.x/maven-artifacts.html

```
<dependencies>
  
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <!-- http://logging.apache.org/log4j/2.x/maven-artifacts.html log4j2 start-->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.0-beta9</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
        <version>2.0-beta9</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-slf4j-impl</artifactId>
        <version>2.0-beta9</version>
    </dependency>
    <!--  log4j2 end -->

</dependencies>
  
```

#### 配置log4j2.xml 放置于项目 classPath 目录


###### log4j2.xml
```
<?xml version="1.0" encoding="UTF-8"?>
	<Configuration status="WARN">
	  <Appenders>
	    <Console name="Console" target="SYSTEM_OUT">
	      <PatternLayout pattern="global %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
	    </Console>
	    <Console name="foo" target="SYSTEM_OUT">
	      <PatternLayout pattern="foo %d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
	    </Console>
	  </Appenders>
	  <Loggers>
	  	<Logger name="com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo" level="trace" additivity="false">
	      <AppenderRef ref="foo"/>
	    </Logger>
	    <Root level="warn">
	      <AppenderRef ref="Console"/>
	    </Root>
	  </Loggers>
	</Configuration>
```
###### 配置说明

- Appenders  定义日志输出方式
- Loggers   定义日志段
    - ROOT 根 ,如果没有匹配到的日志段采用该配置
    - Logger 自定义一个日志段 name 指定 日志段的名称，改名称由 LoggerFactory.getLogger(==name==); 传入
        -  AppenderRef 指定输出目标 ref 的名称与 Appenders 里面的配置段相匹配

#### 创建演示代码

###### 包：com.izhengyin.maven


```
package com.izhengyin.maven;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
/**
 * 
 * @author zhengyin
 * 
 *
 */
public class Slf4jDemo {
	private static Logger logger = LoggerFactory.getLogger(Slf4jDemo.class);
	public static void main(String[] args) {
		// 记录trace信息
		logger.trace("[trace message]");
		// 记录deubg信息
		logger.debug("[debug message]");
		// 记录info，还可以传入参数
		logger.info("[info message]{},{},{},{}", "abc", false, 123, new Slf4jDemo());
		// 记录警告
		logger.warn("[warn message]");
		// 记录error信息
		logger.error("[info messagejkjj]");
		System.out.println("isTraceEnabled"+logger.isTraceEnabled());
		System.out.println("isDebugEnabled"+logger.isDebugEnabled());
		System.out.println("isInfoEnabled"+logger.isInfoEnabled());
		System.out.println("isWarnEnabled"+logger.isWarnEnabled());
		System.out.println("isErrorEnabled"+logger.isErrorEnabled());
		System.out.println("hello world");
		new Slf4jDemoFoo().write();
	}
	static class Slf4jDemoFoo{
		private static Logger logger = LoggerFactory.getLogger(Slf4jDemoFoo.class);
		public void write(){
			System.out.println("------------Slf4jDemoFoo----------");
			// 记录trace信息
			logger.trace("[trace message]");
			// 记录deubg信息
			logger.debug("[debug message]");
			// 记录info，还可以传入参数
			logger.info("[info message]{},{},{},{}", "abc", false, 123, new Slf4jDemo());
			// 记录警告
			logger.warn("[warn message]");
			// 记录error信息
			logger.error("[info messagejkjj]");
			System.out.println("isTraceEnabled"+logger.isTraceEnabled());
			System.out.println("isDebugEnabled"+logger.isDebugEnabled());
			System.out.println("isInfoEnabled"+logger.isInfoEnabled());
			System.out.println("isWarnEnabled"+logger.isWarnEnabled());
			System.out.println("isErrorEnabled"+logger.isErrorEnabled());
			System.out.println("hello world");
		}
	}
}

```

#### 运行演示代码

###### 输出结果

```
global 21:54:58.824 [main] WARN  com.izhengyin.maven.Slf4jDemo - [warn message]
global 21:54:58.825 [main] ERROR com.izhengyin.maven.Slf4jDemo - [info messagejkjj]
isTraceEnabledfalse
isDebugEnabledfalse
isInfoEnabledfalse
isWarnEnabledtrue
isErrorEnabledtrue
hello world
------------Slf4jDemoFoo----------
foo 21:54:58.826 [main] TRACE com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo - [trace message]
foo 21:54:58.826 [main] DEBUG com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo - [debug message]
foo 21:54:58.826 [main] INFO  com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo - [info message]abc,false,123,com.izhengyin.maven.Slf4jDemo@140138b
foo 21:54:58.826 [main] WARN  com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo - [warn message]
foo 21:54:58.826 [main] ERROR com.izhengyin.maven.Slf4jDemo$Slf4jDemoFoo - [info messagejkjj]
isTraceEnabledtrue
isDebugEnabledtrue
isInfoEnabledtrue
isWarnEnabledtrue
isErrorEnabledtrue
hello world
```

