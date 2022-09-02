
# MyBatis简介

## 1、MyBatis历史

MyBatis最初是Apache的一个开源项目iBatis,2010年6月这个项目由Apache Software Foundation迁移到了Google Code。随着开发团队转投Google Code旗下，iBatis3.x正式更名为MyBatis。代码于2013年11月迁移到Github。

iBatis一词来源于"internet"和" abatis"的组合，是一个基于Java的持久层框架。iBatis提供的持久层框架包括SQLMaps和Data Access Objects (DAO)。

## 2、MyBatis特性

1) MyBatis是支持定制化SQL、存储过程以及高级映射的优秀的持久层框架

**2) MyBatis 避免了几乎所有的JDBC代码和手动设置参数以及获取结果集**

3) MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO(Plain Old Java Objects，普通的Java对象）映射成数据库中的记录

4) MyBatis是一个半自动的ORM (Object Relation Mapping)框架

## 3、MyBatis下载

MyBatis下载地址: https://github.com/mybatis/mybatis-3
### 加入log4j日志功能
1. 加入依赖
	```xml
	<!-- log4j日志 -->
	<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId>
	<version>1.2.17</version>
	</dependency>
	```
2. 加入log4j的配置文件
	- log4j的配置文件名为log4j.xml，
	- 存放的位置是src/main/resources目录下
####	- 日志的级别：
	- FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试) 从左到右打印的内容越来越详细
### 核心配置文件详解
>核心配置文件中的标签必须按照固定的顺序(有的标签可以不写，但顺序一定不能乱)：
properties、settings、typeAliases、typeHandlers、objectFactory、objectWrapperFactory、reflectorFactory、plugins、environments、databaseIdProvider、mappers
# MyBatis获取参数值的两种方式（重点）
- MyBatis获取参数值的两种方式：${}和#{}  
- ${}的本质就是字符串拼接，#{}的本质就是占位符赋值  
- ${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号；但是#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号
## 单个字面量类型的参数
- 若mapper接口中的方法参数为单个的字面量类型，此时可以使用\${}和#{}以任意的名称（最好见名识意）获取参数的值，注意${}需要手动加单引号
-  
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExMTY0OTgyNDEsMTI5MTY5ODE4LDIwMz
A0NTIwNTcsLTc0NTAwNzczM119
-->