---
title: "基于SSH的员工管理系统（二）——lib详解"
date: 2016-11-02T12:55:29+00:00
tags: ["java"]
---

## lib导入各jar包详解
<pre>
	本节将关于本项目所需导入的jar包进行逐一解释，基本适用于普通的Struts2+Hibernate3+Spring3的项目， 此为相对较老的组合版本，若使用Hibernate4或Mybatis等其他较新框架，请自行查阅相关资料。
</pre>
### 1、struts2基础必备包（解压blank.war可得）
#### 本项目使用struts2.3.4.1
![这里写图片描述](20161102112001442.png)

附加Jar解释：

 - struts2-convention-plugin-2.3.4.1.jar——支持struts2的注解开发
<pre>
ssh三大框架都支持注解开发，ssh整合有三种方式：
	
 1. 带有Hibernate配置文件（hibernate.cfg.xml）
 2. 不带有Hibernate配置文件（本项目所采用）
 3. 纯注解开发
</pre>
- struts2-spring-plugin-2.3.4.1.jar——用于整合spring
<pre>
spring的jar包中也有一个spring-struts2-plugin.jar，和该包一样，引入其一即可。
</pre>
### 2、hibernate3基础必备包

#### 本项目使用hibernate3.3.1
![这里写图片描述](20161102120954010.png)

- 根路径hibernate3.jar——核心jar包
- required目录所有jar包
- hibernate日志记录——slf4j-log4j.jar
- 数据库驱动包——mysql-connector-java.jar（本项目使用mysql数据库）
<pre>
hibernate中已引入slf4j（slf4j-api.jar）,但其不作具体日志记录,所以需要引入slf4j-log4j.jar整合log4j
</pre>

### 3、spring基础必备包
<pre>
	根据具体开发情况引入IoC或AOP的jar包
</pre>
#### 本项目使用spring3.2.2
![这里写图片描述](20161102122140263.png)

spring基本jar包包括

 1. IoC开发
- spring-beans.jar
- spring-context.jar
- spring-core.jar
- spring-expression.jar
- com.springsource.org.apache.log4j.jar——作日志记录
- com.springsource.org.apache.commons.logging.jar——日志整合，不作具体日志记录，用于整合其他日志系统
 2. AOP
- spring-aop.jar
- spring-aspect.jar——整合aspect
- com.spinrgsource.org.aopalliance.jar——aop联盟
- com.springsource.org.aspectj.weaver.jar
 3. 其他
- spring-tx.jar——事务管理
- spring-jdbc.jar——jdbc模板
- spring-orm.jar——整合hibernate
- spring-web.jar——整合web项目
- spring-test.jar——整合JUnit单元测试
- c3p0.jar——c3p0连接池

> 在实际开发中，一开始的jar包导入环节总是没有清晰的头绪，原因就在于实际开发经验不足，对框架理解不够，此处就体现出阅读框架源码的重要性。

> “框架官网文档就是学习框架的最好资料”  与君共勉

- [struts官方文档](http://struts.apache.org/maven/struts2-core/apidocs/index.html)
- [hibernate官方文档](http://docs.jboss.org/hibernate/orm/5.2/javadocs/)
- [spring官方文档](http://docs.spring.io/spring/docs/5.0.0.M2/javadoc-api/)