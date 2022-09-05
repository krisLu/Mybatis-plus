
# Mybatis简介
## MyBatis历史
-    MyBatis最初是Apache的一个开源项目iBatis, 2010年6月这个项目由Apache Software Foundation迁移到了Google Code。随着开发团队转投Google Code旗下，iBatis3.x正式更名为MyBatis。代码于2013年11月迁移到Github
- iBatis一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。iBatis提供的持久层框架包括SQL Maps和Data Access Objects（DAO）
## MyBatis特性
1. MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架
2. MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集
3. MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录
4. MyBatis 是一个 半自动的ORM（Object Relation Mapping）框架
## MyBatis下载
- [MyBatis下载地址(https://github.com/mybatis/mybatis-3)
 ![](Resources/MyBatis下载.png)
## 和其它持久化层技术对比
- JDBC  
	- SQL 夹杂在Java代码中耦合度高，导致硬编码内伤  
	- 维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见  
	- 代码冗长，开发效率低
- Hibernate 和 JPA
	- 操作简便，开发效率高  
	- 程序中的长难复杂 SQL 需要绕过框架  
	- 内部自动生产的 SQL，不容易做特殊优化  
	- 基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。  
	- 反射操作太多，导致数据库性能下降
- MyBatis
	- 轻量级，性能出色  
	- SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据  
	- 开发效率稍逊于HIbernate，但是完全能够接受
# 搭建MyBatis
## 开发环境
- IDE：idea 2019.2  
- 构建工具：maven 3.5.4  
- MySQL版本：MySQL 5.7  
- MyBatis版本：MyBatis 3.5.7
## 创建maven工程
- 打包方式：jar
- 引入依赖

	```xml
	<dependencies>
		<!-- Mybatis核心 -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.5.7</version>
		</dependency>
		<!-- junit测试 -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<!-- MySQL驱动 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.3</version>
			</dependency>
	</dependencies>
	```
## 创建MyBatis的核心配置文件
>习惯上命名为`mybatis-config.xml`，这个文件名仅仅只是建议，并非强制要求。将来整合Spring之后，这个配置文件可以省略，所以大家操作时可以直接复制、粘贴。
>核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息
>核心配置文件存放的位置是src/main/resources目录下
```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration  
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-config.dtd">  
<configuration>  
	<!--设置连接数据库的环境-->  
	<environments default="development">  
		<environment id="development">  
			<transactionManager type="JDBC"/>  
			<dataSource type="POOLED">  
				<property name="driver" value="com.mysql.cj.jdbc.Driver"/>  
				<property name="url" value="jdbc:mysql://localhost:3306/MyBatis"/>  
				<property name="username" value="root"/>  
				<property name="password" value="123456"/>  
			</dataSource>  
		</environment>  
	</environments>  
	<!--引入映射文件-->  
	<mappers>  
		<mapper resource="mappers/UserMapper.xml"/>  
	</mappers>  
</configuration>
```
## 创建mapper接口
>MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类
```java
package com.atguigu.mybatis.mapper;  
  
public interface UserMapper {  
	/**  
	* 添加用户信息  
	*/  
	int insertUser();  
}
```
## 创建MyBatis的映射文件
- 相关概念：ORM（Object Relationship Mapping）对象关系映射。  
	- 对象：Java的实体类对象  
	- 关系：关系型数据库  
	- 映射：二者之间的对应关系

| Java概念 | 数据库概念 |
| --- | --- |
| 类 | 表 |
| 属性 | 字段/列 |
| 对象 | 记录/行 |

- 映射文件的命名规则
	- 表所对应的实体类的类名+Mapper.xml
	- 例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml 
	- 因此一个映射文件对应一个实体类，对应一张表的操作
	- MyBatis映射文件用于编写SQL，访问以及操作表中的数据
	- MyBatis映射文件存放的位置是src/main/resources/mappers目录下
- MyBatis中可以面向接口操作数据，要保证两个一致
	- mapper接口的全类名和映射文件的命名空间（namespace）保持一致
	- mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致
```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE mapper  
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">  
<mapper namespace="com.atguigu.mybatis.mapper.UserMapper">  
	<!--int insertUser();-->  
	<insert id="insertUser">  
		insert into t_user values(null,'张三','123',23,'女')  
	</insert>  
</mapper>
```
## 通过junit测试功能
- SqlSession：代表Java程序和数据库之间的会话。（HttpSession是Java程序和浏览器之间的会话）
- SqlSessionFactory：是“生产”SqlSession的“工厂”
- 工厂模式：如果创建某一个对象，使用的过程基本固定，那么我们就可以把创建这个对象的相关代码封装到一个“工厂类”中，以后都使用这个工厂类来“生产”我们需要的对象
```java
public class UserMapperTest {
    @Test
    public void testInsertUser() throws IOException {
        //读取MyBatis的核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        //获取SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        //通过核心配置文件所对应的字节输入流创建工厂类SqlSessionFactory，生产SqlSession对象
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        //获取sqlSession，此时通过SqlSession对象所操作的sql都必须手动提交或回滚事务
        //SqlSession sqlSession = sqlSessionFactory.openSession();
	    //创建SqlSession对象，此时通过SqlSession对象所操作的sql都会自动提交  
		SqlSession sqlSession = sqlSessionFactory.openSession(true);
        //通过代理模式创建UserMapper接口的代理实现类对象
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        //调用UserMapper接口中的方法，就可以根据UserMapper的全类名匹配元素文件，通过调用的方法名匹配映射文件中的SQL标签，并执行标签中的SQL语句
        int result = userMapper.insertUser();
        //提交事务
        //sqlSession.commit();
        System.out.println("result:" + result);
    }
}
```
- 此时需要手动提交事务，如果要自动提交事务，则在获取sqlSession对象时，使用`SqlSession sqlSession = sqlSessionFactory.openSession(true);`，传入一个Boolean类型的参数，值为true，这样就可以自动提交
## 加入log4j日志功能
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
	- log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下
	- 日志的级别：FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试) 从左到右打印的内容越来越详细
	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
	<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
	    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
	        <param name="Encoding" value="UTF-8" />
	        <layout class="org.apache.log4j.PatternLayout">
				<param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" />
	        </layout>
	    </appender>
	    <logger name="java.sql">
	        <level value="debug" />
	    </logger>
	    <logger name="org.apache.ibatis">
	        <level value="info" />
	    </logger>
	    <root>
	        <level value="debug" />
	        <appender-ref ref="STDOUT" />
	    </root>
	</log4j:configuration>
	```
# 核心配置文件详解
>核心配置文件中的标签必须按照固定的顺序(有的标签可以不写，但顺序一定不能乱)：
properties、settings、typeAliases、typeHandlers、objectFactory、objectWrapperFactory、reflectorFactory、plugins、environments、databaseIdProvider、mappers
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//MyBatis.org//DTD Config 3.0//EN"
        "http://MyBatis.org/dtd/MyBatis-3-config.dtd">
<configuration>
    <!--引入properties文件，此时就可以${属性名}的方式访问属性值-->
    <properties resource="jdbc.properties"></properties>
    <settings>
        <!--将表中字段的下划线自动转换为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
    </settings>
    <typeAliases>
        <!--
        typeAlias：设置某个具体的类型的别名
        属性：
        type：需要设置别名的类型的全类名
        alias：设置此类型的别名，且别名不区分大小写。若不设置此属性，该类型拥有默认的别名，即类名
        -->
        <!--<typeAlias type="com.atguigu.mybatis.bean.User"></typeAlias>-->
        <!--<typeAlias type="com.atguigu.mybatis.bean.User" alias="user">
        </typeAlias>-->
        <!--以包为单位，设置改包下所有的类型都拥有默认的别名，即类名且不区分大小写-->
        <package name="com.atguigu.mybatis.bean"/>
    </typeAliases>
    <!--
    environments：设置多个连接数据库的环境
    属性：
	    default：设置默认使用的环境的id
    -->
    <environments default="mysql_test">
        <!--
        environment：设置具体的连接数据库的环境信息
        属性：
	        id：设置环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境
        -->
        <environment id="mysql_test">
            <!--
            transactionManager：设置事务管理方式
            属性：
	            type：设置事务管理方式，type="JDBC|MANAGED"
	            type="JDBC"：设置当前环境的事务管理都必须手动处理
	            type="MANAGED"：设置事务被管理，例如spring中的AOP
            -->
            <transactionManager type="JDBC"/>
            <!--
            dataSource：设置数据源
            属性：
	            type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
	            type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建
	            type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
	            type="JNDI"：调用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <!--设置驱动类的全类名-->
                <property name="driver" value="${jdbc.driver}"/>
                <!--设置连接数据库的连接地址-->
                <property name="url" value="${jdbc.url}"/>
                <!--设置连接数据库的用户名-->
                <property name="username" value="${jdbc.username}"/>
                <!--设置连接数据库的密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <!-- <mapper resource="UserMapper.xml"/> -->
        <!--
        以包为单位，将包下所有的映射文件引入核心配置文件
        注意：
			1. 此方式必须保证mapper接口和mapper映射文件必须在相同的包下
			2. mapper接口要和mapper映射文件的名字一致
        -->
        <package name="com.atguigu.mybatis.mapper"/>
    </mappers>
</configuration>
```
# MyBatis的增删改查
1. 添加
	```xml
	<!--int insertUser();-->
	<insert id="insertUser">
		insert into t_user values(null,'admin','123456',23,'男','12345@qq.com')
	</insert>
	```
2. 删除
	```xml
	<!--int deleteUser();-->
    <delete id="deleteUser">
        delete from t_user where id = 6
    </delete>
	```
3. 修改
	```xml
	<!--int updateUser();-->
    <update id="updateUser">
        update t_user set username = '张三' where id = 5
    </update>
	```
4. 查询一个实体类对象
	```xml
   <!--User getUserById();-->  
	<select id="getUserById" resultType="com.atguigu.mybatis.bean.User">  
		select * from t_user where id = 2  
	</select>
	```
5. 查询集合
	```xml
	<!--List<User> getUserList();-->
	<select id="getUserList" resultType="com.atguigu.mybatis.bean.User">
		select * from t_user
	</select>
	```
- 注意：

	1. 查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系  
		- resultType：自动映射，用于属性名和表中字段名一致的情况  
		- resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况  
	2. 当查询的数据为多条时，不能使用实体类作为返回值，只能使用集合，否则会抛出异常TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值
# MyBatis获取参数值的两种方式（重点）
- MyBatis获取参数值的两种方式：${}和#{}  
- ${}的本质就是字符串拼接，#{}的本质就是占位符赋值  
- ${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号；但是#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号
- ${} : 本质字符串拼接，存在sql注入问题
- #{}： 本质是占位符赋值。
## 单个字面量类型的参数
- 若mapper接口中的方法参数为单个的字面量类型，此时可以使用\${}和#{}以任意的名称（最好见名识意）获取参数的值，注意${}**需要手动加单引号**
```xml
<!--User getUserByUsername(String username);-->
<select id="getUserByUsername" resultType="User">
	select * from t_user where username = #{username}
</select>
```
```xml
<!--User getUserByUsername(String username);-->
<select id="getUserByUsername" resultType="User">  
	select * from t_user where username = '${username}'  
</select>
```
## 多个字面量类型的参数
- 若mapper接口中的方法参数为多个时，此时MyBatis会自动将这些参数放在一个map集合中

	1. 以arg0,arg1...为键，以参数为值；
	2. 以param1,param2...为键，以参数为值；
- 因此只需要通过\${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号。
- 使用arg或者param都行，要注意的是，arg是从arg0开始的，param是从param1开始的
```xml
<!--User checkLogin(String username,String password);-->
<select id="checkLogin" resultType="User">  
	select * from t_user where username = #{arg0} and password = #{arg1}  
</select>
```
```xml
<!--User checkLogin(String username,String password);-->
<select id="checkLogin" resultType="User">
	select * from t_user where username = '${param1}' and password = '${param2}'
</select>
```
## map集合类型的参数
- 若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中只需要通过\${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号
```xml
<!--User checkLoginByMap(Map<String,Object> map);-->
<select id="checkLoginByMap" resultType="User">
	select * from t_user where username = #{username} and password = #{password}
</select>
```
```java
@Test
public void checkLoginByMap() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
	Map<String,Object> map = new HashMap<>();
	map.put("usermane","admin");
	map.put("password","123456");
	User user = mapper.checkLoginByMap(map);
	System.out.println(user);
}
```
## 实体类类型的参数
- 若mapper接口中的方法参数为实体类对象时此时可以使用\${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号
```xml
<!--int insertUser(User user);-->
<insert id="insertUser">
	insert into t_user values(null,#{username},#{password},#{age},#{sex},#{email})
</insert>
```
```java
@Test
public void insertUser() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
	User user = new User(null,"Tom","123456",12,"男","123@321.com");
	mapper.insertUser(user);
}
```
## 使用@Param标识参数
- 可以通过@Param注解标识mapper接口中的方法参数，此时，会将这些参数放在map集合中 

	1. 以@Param注解的value属性值为键，以参数为值；
	2. 以param1,param2...为键，以参数为值；
- 只需要通过\${}和#{}访问map集合的键就可以获取相对应的值，注意${}需要手动加单引号
```xml
<!--User CheckLoginByParam(@Param("username") String username, @Param("password") String password);-->
    <select id="CheckLoginByParam" resultType="User">
        select * from t_user where username = #{username} and password = #{password}
    </select>
```
```java
@Test
public void checkLoginByParam() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	ParameterMapper mapper = sqlSession.getMapper(ParameterMapper.class);
	mapper.CheckLoginByParam("admin","123456");
}
```
## 总结
- 建议分成两种情况进行处理

	1. 实体类类型的参数
	2. 使用@Param标识参数
# MyBatis的各种查询功能
1. 如果查询出的数据只有一条，可以通过
	1. 实体类对象接收
	2. List集合接收
	3. Map集合接收，结果`{password=123456, sex=男, id=1, age=23, username=admin}`
2. 如果查询出的数据有多条，一定不能用实体类对象接收，会抛异常TooManyResultsException，可以通过
	1. 实体类类型的LIst集合接收
	2. Map类型的LIst集合接收
	3. 在mapper接口的方法上添加@MapKey注解
## 查询一个实体类对象
```java
/**
 * 根据用户id查询用户信息
 * @param id
 * @return
 */
User getUserById(@Param("id") int id);
```
```xml
<!--User getUserById(@Param("id") int id);-->
<select id="getUserById" resultType="User">
	select * from t_user where id = #{id}
</select>
```
## 查询一个List集合
```java
/**
 * 查询所有用户信息
 * @return
 */
List<User> getUserList();
```
```xml
<!--List<User> getUserList();-->
<select id="getUserList" resultType="User">
	select * from t_user
</select>
```
## 查询单个数据
```java
/**  
 * 查询用户的总记录数  
 * @return  
 * 在MyBatis中，对于Java中常用的类型都设置了类型别名  
 * 例如：java.lang.Integer-->int|integer  
 * 例如：int-->_int|_integer  
 * 例如：Map-->map,List-->list  
 */  
int getCount();
```
```xml
<!--int getCount();-->
<select id="getCount" resultType="_integer">
	select count(id) from t_user
</select>
```
## 查询一条数据为map集合
```java
/**  
 * 根据用户id查询用户信息为map集合  
 * @param id  
 * @return  
 */  
Map<String, Object> getUserToMap(@Param("id") int id);
```
```xml
<!--Map<String, Object> getUserToMap(@Param("id") int id);-->
<select id="getUserToMap" resultType="map">
	select * from t_user where id = #{id}
</select>
<!--结果：{password=123456, sex=男, id=1, age=23, username=admin}-->
```
## 查询多条数据为map集合
### 方法一
```java
/**  
 * 查询所有用户信息为map集合  
 * @return  
 * 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，此时可以将这些map放在一个list集合中获取  
 */  
List<Map<String, Object>> getAllUserToMap();
```
```xml
<!--Map<String, Object> getAllUserToMap();-->  
<select id="getAllUserToMap" resultType="map">  
	select * from t_user  
</select>
<!--
	结果：
	[{password=123456, sex=男, id=1, age=23, username=admin},
	{password=123456, sex=男, id=2, age=23, username=张三},
	{password=123456, sex=男, id=3, age=23, username=张三}]
-->
```
### 方法二
```java
/**
 * 查询所有用户信息为map集合
 * @return
 * 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置map集合的键，值是每条数据所对应的map集合
 */
@MapKey("id")
Map<String, Object> getAllUserToMap();
```
```xml
<!--Map<String, Object> getAllUserToMap();-->
<select id="getAllUserToMap" resultType="map">
	select * from t_user
</select>
<!--
	结果：
	{
	1={password=123456, sex=男, id=1, age=23, username=admin},
	2={password=123456, sex=男, id=2, age=23, username=张三},
	3={password=123456, sex=男, id=3, age=23, username=张三}
	}
-->
```
# 特殊SQL的执行
## 模糊查询
```java
/**
 * 根据用户名进行模糊查询
 * @param username 
 * @return java.util.List<com.atguigu.mybatis.pojo.User>
 * @date 2022/2/26 21:56
 */
List<User> getUserByLike(@Param("username") String username);
```
```xml
<!--List<User> getUserByLike(@Param("username") String username);-->
<select id="getUserByLike" resultType="User">
	<!--select * from t_user where username like '%${mohu}%'-->  
	<!--select * from t_user where username like concat('%',#{mohu},'%')-->  
	select * from t_user where username like "%"#{mohu}"%"
</select>
```
- 其中`select * from t_user where username like "%"#{mohu}"%"`是最常用的
## 批量删除
- 只能使用\${}，如果使用#{}，则解析后的sql语句为`delete from t_user where id in ('1,2,3')`，这样是将`1,2,3`看做是一个整体，只有id为`1,2,3`的数据会被删除。正确的语句应该是`delete from t_user where id in (1,2,3)`，或者`delete from t_user where id in ('1','2','3')`
```java
/**
 * 根据id批量删除
 * @param ids 
 * @return int
 * @date 2022/2/26 22:06
 */
int deleteMore(@Param("ids") String ids);
```
```xml
<delete id="deleteMore">
	delete from t_user where id in (${ids})
</delete>
```
```java
//测试类
@Test
public void deleteMore() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
	int result = mapper.deleteMore("1,2,3,8");
	System.out.println(result);
}
```
## 动态设置表名
- 只能使用${}，因为表名不能加单引号
```java
/**
 * 查询指定表中的数据
 * @param tableName 
 * @return java.util.List<com.atguigu.mybatis.pojo.User>
 * @date 2022/2/27 14:41
 */
List<User> getUserByTable(@Param("tableName") String tableName);
```
```xml
<!--List<User> getUserByTable(@Param("tableName") String tableName);-->
<select id="getUserByTable" resultType="User">
	select * from ${tableName}
</select>
```
## 添加功能获取自增的主键
- 使用场景
	- t_clazz(clazz_id,clazz_name)  
	- t_student(student_id,student_name,clazz_id)  
	1. 添加班级信息  
	2. 获取新添加的班级的id  
	3. 为班级分配学生，即将某学的班级id修改为新添加的班级的id
- 在mapper.xml中设置两个属性
	- useGeneratedKeys：设置使用自增的主键  
	* keyProperty：因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参数user对象的某个属性中
```java
/**
 * 添加用户信息
 * @param user 
 * @date 2022/2/27 15:04
 */
void insertUser(User user);
```
```xml
<!--void insertUser(User user);-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
	insert into t_user values (null,#{username},#{password},#{age},#{sex},#{email})
</insert>
```
```java
//测试类
@Test
public void insertUser() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
	User user = new User(null, "ton", "123", 23, "男", "123@321.com");
	mapper.insertUser(user);
	System.out.println(user);
	//输出：user{id=10, username='ton', password='123', age=23, sex='男', email='123@321.com'}，自增主键存放到了user的id属性中
}
```
# 自定义映射resultMap
## resultMap处理字段和属性的映射关系
- resultMap：设置自定义映射  
	- 属性：  
		- id：表示自定义映射的唯一标识，不能重复
		- type：查询的数据要映射的实体类的类型  
	- 子标签：  
		- id：设置主键的映射关系  
		- result：设置普通字段的映射关系  
		- 子标签属性：  
			- property：设置映射关系中实体类中的属性名  
			- column：设置映射关系中表中的字段名
- 若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射，即使字段名和属性名一致的属性也要映射，也就是全部属性都要列出来
```xml
<resultMap id="empResultMap" type="Emp">
	<id property="eid" column="eid"></id>
	<result property="empName" column="emp_name"></result>
	<result property="age" column="age"></result>
	<result property="sex" column="sex"></result>
	<result property="email" column="email"></result>
</resultMap>
<!--List<Emp> getAllEmp();-->
<select id="getAllEmp" resultMap="empResultMap">
	select * from t_emp
</select>
```
- 若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性名符合Java的规则（使用驼峰）。此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系  

	1. 可以通过为字段起别名的方式，保证和实体类中的属性名保持一致  
		```xml
		<!--List<Emp> getAllEmp();-->
		<select id="getAllEmp" resultType="Emp">
			select eid,emp_name empName,age,sex,email from t_emp
		</select>
		```
	2. 可以在MyBatis的核心配置文件中的`setting`标签中，设置一个全局配置信息mapUnderscoreToCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰，例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为userName。[核心配置文件详解](#核心配置文件详解)
		```xml
	<settings>
	    <setting name="mapUnderscoreToCamelCase" value="true"/>
	</settings>
		```
## 多对一映射处理
>查询员工信息以及员工所对应的部门信息
```java
public class Emp {  
	private Integer eid;  
	private String empName;  
	private Integer age;  
	private String sex;  
	private String email;  
	private Dept dept;
	//...构造器、get、set方法等
}
```
### 级联方式处理映射关系
```xml
<resultMap id="empAndDeptResultMapOne" type="Emp">
	<id property="eid" column="eid"></id>
	<result property="empName" column="emp_name"></result>
	<result property="age" column="age"></result>
	<result property="sex" column="sex"></result>
	<result property="email" column="email"></result>
	<result property="dept.did" column="did"></result>
	<result property="dept.deptName" column="dept_name"></result>
</resultMap>
<!--Emp getEmpAndDept(@Param("eid")Integer eid);-->
<select id="getEmpAndDept" resultMap="empAndDeptResultMapOne">
	select * from t_emp left join t_dept on t_emp.eid = t_dept.did where t_emp.eid = #{eid}
</select>
```
### 使用association处理映射关系
- association：处理多对一的映射关系
- property：需要处理多对的映射关系的属性名
- javaType：该属性的类型
```xml
<resultMap id="empAndDeptResultMapTwo" type="Emp">
	<id property="eid" column="eid"></id>
	<result property="empName" column="emp_name"></result>
	<result property="age" column="age"></result>
	<result property="sex" column="sex"></result>
	<result property="email" column="email"></result>
	<association property="dept" javaType="Dept">
		<id property="did" column="did"></id>
		<result property="deptName" column="dept_name"></result>
	</association>
</resultMap>
<!--Emp getEmpAndDept(@Param("eid")Integer eid);-->
<select id="getEmpAndDept" resultMap="empAndDeptResultMapTwo">
	select * from t_emp left join t_dept on t_emp.eid = t_dept.did where t_emp.eid = #{eid}
</select>
```
### 分步查询
#### 1. 查询员工信息
- select：设置分布查询的sql的唯一标识（namespace.SQLId或mapper接口的全类名.方法名）
- column：设置分步查询的条件
```java
//EmpMapper里的方法
/**
 * 通过分步查询，员工及所对应的部门信息
 * 分步查询第一步：查询员工信息
 * @param  
 * @return com.atguigu.mybatis.pojo.Emp
 * @date 2022/2/27 20:17
 */
Emp getEmpAndDeptByStepOne(@Param("eid") Integer eid);
```
```xml
<resultMap id="empAndDeptByStepResultMap" type="Emp">
	<id property="eid" column="eid"></id>
	<result property="empName" column="emp_name"></result>
	<result property="age" column="age"></result>
	<result property="sex" column="sex"></result>
	<result property="email" column="email"></result>
	<association property="dept"
				 select="com.atguigu.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
				 column="did"></association>
</resultMap>
<!--Emp getEmpAndDeptByStepOne(@Param("eid") Integer eid);-->
<select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
	select * from t_emp where eid = #{eid}
</select>
```
#### 2. 查询部门信息
```java
//DeptMapper里的方法
/**
 * 通过分步查询，员工及所对应的部门信息
 * 分步查询第二步：通过did查询员工对应的部门信息
 * @param
 * @return com.atguigu.mybatis.pojo.Emp
 * @date 2022/2/27 20:23
 */
Dept getEmpAndDeptByStepTwo(@Param("did") Integer did);
```
```xml
<!--此处的resultMap仅是处理字段和属性的映射关系-->
<resultMap id="EmpAndDeptByStepTwoResultMap" type="Dept">
	<id property="did" column="did"></id>
	<result property="deptName" column="dept_name"></result>
</resultMap>
<!--Dept getEmpAndDeptByStepTwo(@Param("did") Integer did);-->
<select id="getEmpAndDeptByStepTwo" resultMap="EmpAndDeptByStepTwoResultMap">
	select * from t_dept where did = #{did}
</select>
```
## 一对多映射处理
```java
public class Dept {
    private Integer did;
    private String deptName;
    private List<Emp> emps;
	//...构造器、get、set方法等
}
```
### collection
- collection：用来处理一对多的映射关系
- ofType：表示该属性对饮的集合中存储的数据的类型
```xml
<resultMap id="DeptAndEmpResultMap" type="Dept">
	<id property="did" column="did"></id>
	<result property="deptName" column="dept_name"></result>
	<collection property="emps" ofType="Emp">
		<id property="eid" column="eid"></id>
		<result property="empName" column="emp_name"></result>
		<result property="age" column="age"></result>
		<result property="sex" column="sex"></result>
		<result property="email" column="email"></result>
	</collection>
</resultMap>
<!--Dept getDeptAndEmp(@Param("did") Integer did);-->
<select id="getDeptAndEmp" resultMap="DeptAndEmpResultMap">
	select * from t_dept left join t_emp on t_dept.did = t_emp.did where t_dept.did = #{did}
</select>
```
### 分步查询
####  1. 查询部门信息
```java
/**
 * 通过分步查询，查询部门及对应的所有员工信息
 * 分步查询第一步：查询部门信息
 * @param did 
 * @return com.atguigu.mybatis.pojo.Dept
 * @date 2022/2/27 22:04
 */
Dept getDeptAndEmpByStepOne(@Param("did") Integer did);
```
```xml
<resultMap id="DeptAndEmpByStepOneResultMap" type="Dept">
	<id property="did" column="did"></id>
	<result property="deptName" column="dept_name"></result>
	<collection property="emps"
				select="com.atguigu.mybatis.mapper.EmpMapper.getDeptAndEmpByStepTwo"
				column="did"></collection>
</resultMap>
<!--Dept getDeptAndEmpByStepOne(@Param("did") Integer did);-->
<select id="getDeptAndEmpByStepOne" resultMap="DeptAndEmpByStepOneResultMap">
	select * from t_dept where did = #{did}
</select>
```
#### 2. 根据部门id查询部门中的所有员工
```java
/**
 * 通过分步查询，查询部门及对应的所有员工信息
 * 分步查询第二步：根据部门id查询部门中的所有员工
 * @param did
 * @return java.util.List<com.atguigu.mybatis.pojo.Emp>
 * @date 2022/2/27 22:10
 */
List<Emp> getDeptAndEmpByStepTwo(@Param("did") Integer did);
```
```xml
<!--List<Emp> getDeptAndEmpByStepTwo(@Param("did") Integer did);-->
<select id="getDeptAndEmpByStepTwo" resultType="Emp">
	select * from t_emp where did = #{did}
</select>
```
## 延迟加载
- 分步查询的优点：可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息：
	- lazyLoadingEnabled：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载  
	- aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性。 否则，每个属性会按需加载  
- 此时就可以实现按需加载，获取的数据是什么，就只会执行相应的sql。此时可通过association和collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy(延迟加载)|eager(立即加载)"
```xml
<settings>
	<!--开启延迟加载-->
	<setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

```java
@Test
public void getEmpAndDeptByStepOne() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	Emp emp = mapper.getEmpAndDeptByStepOne(1);
	System.out.println(emp.getEmpName());
}
```
- 关闭延迟加载，两条SQL语句都运行了![](Resources/延迟加载测试1.png)
- 开启延迟加载，只运行获取emp的SQL语句
![](Resources/延迟加载测试2.png)
```java
@Test
public void getEmpAndDeptByStepOne() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	Emp emp = mapper.getEmpAndDeptByStepOne(1);
	System.out.println(emp.getEmpName());
	System.out.println("----------------");
	System.out.println(emp.getDept());
}
```
- 开启后，需要用到查询dept的时候才会调用相应的SQL语句![](Resources/延迟加载测试3.png)
- fetchType：当开启了全局的延迟加载之后，可以通过该属性手动控制延迟加载的效果，fetchType="lazy(延迟加载)|eager(立即加载)"

	```xml
	<resultMap id="empAndDeptByStepResultMap" type="Emp">
		<id property="eid" column="eid"></id>
		<result property="empName" column="emp_name"></result>
		<result property="age" column="age"></result>
		<result property="sex" column="sex"></result>
		<result property="email" column="email"></result>
		<association property="dept"
					 select="com.atguigu.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
					 column="did"
					 fetchType="lazy"></association>
	</resultMap>
	```
# 动态SQL
- Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题
## if
- if标签可通过test属性（即传递过来的数据）的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行
- 在where后面添加一个恒成立条件`1=1`
	- 这个恒成立条件并不会影响查询的结果
	- 这个`1=1`可以用来拼接`and`语句，例如：当empName为null时
		- 如果不加上恒成立条件，则SQL语句为`select * from t_emp where and age = ? and sex = ? and email = ?`，此时`where`会与`and`连用，SQL语句会报错
		- 如果加上一个恒成立条件，则SQL语句为`select * from t_emp where 1= 1 and age = ? and sex = ? and email = ?`，此时不报错
```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp where 1=1
	<if test="empName != null and empName !=''">
		and emp_name = #{empName}
	</if>
	<if test="age != null and age !=''">
		and age = #{age}
	</if>
	<if test="sex != null and sex !=''">
		and sex = #{sex}
	</if>
	<if test="email != null and email !=''">
		and email = #{email}
	</if>
</select>
```
## where
- where和if一般结合使用：
	- 若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字  
	- 若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的and/or去掉  
```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp
	<where>
		<if test="empName != null and empName !=''">
			emp_name = #{empName}
		</if>
		<if test="age != null and age !=''">
			and age = #{age}
		</if>
		<if test="sex != null and sex !=''">
			and sex = #{sex}
		</if>
		<if test="email != null and email !=''">
			and email = #{email}
		</if>
	</where>
</select>
```
- 注意：where标签不能去掉条件后多余的and/or

	```xml
	<!--这种用法是错误的，只能去掉条件前面的and/or，条件后面的不行-->
	<if test="empName != null and empName !=''">
	emp_name = #{empName} and
	</if>
	<if test="age != null and age !=''">
		age = #{age}
	</if>
	```
## trim
- trim用于去掉或添加标签中的内容  
- 常用属性
	- prefix：在trim标签中的内容的前面添加某些内容  
	- suffix：在trim标签中的内容的后面添加某些内容 
	- prefixOverrides：在trim标签中的内容的前面去掉某些内容  
	- suffixOverrides：在trim标签中的内容的后面去掉某些内容
- 若trim中的标签都不满足条件，则trim标签没有任何效果，也就是只剩下`select * from t_emp`
```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select * from t_emp
	<trim prefix="where" suffixOverrides="and|or">
		<if test="empName != null and empName !=''">
			emp_name = #{empName} and
		</if>
		<if test="age != null and age !=''">
			age = #{age} and
		</if>
		<if test="sex != null and sex !=''">
			sex = #{sex} or
		</if>
		<if test="email != null and email !=''">
			email = #{email}
		</if>
	</trim>
</select>
```
```java
//测试类
@Test
public void getEmpByCondition() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
	List<Emp> emps= mapper.getEmpByCondition(new Emp(null, "张三", null, null, null, null));
	System.out.println(emps);
}
```
![](Resources/trim测试结果.png)
## choose、when、otherwise
- `choose、when、otherwise`相当于`if...else if..else`
- when至少要有一个，otherwise至多只有一个
```xml
<select id="getEmpByChoose" resultType="Emp">
	select * from t_emp
	<where>
		<choose>
			<when test="empName != null and empName != ''">
				emp_name = #{empName}
			</when>
			<when test="age != null and age != ''">
				age = #{age}
			</when>
			<when test="sex != null and sex != ''">
				sex = #{sex}
			</when>
			<when test="email != null and email != ''">
				email = #{email}
			</when>
			<otherwise>
				did = 1
			</otherwise>
		</choose>
	</where>
</select>
```
```java
@Test
public void getEmpByChoose() {
	SqlSession sqlSession = SqlSessionUtils.getSqlSession();
	DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
	List<Emp> emps = mapper.getEmpByChoose(new Emp(null, "张三", 23, "男", "123@qq.com", null));
	System.out.println(emps);
}
```
![](Resources/choose测试结果.png)
- 相当于`if a else if b else if c else d`，只会执行其中一个
## foreach
- 属性：  
	- collection：设置要循环的数组或集合  
	- item：表示集合或数组中的每一个数据  
	- separator：设置循环体之间的分隔符，分隔符前后默认有一个空格，如` , `
	- open：设置foreach标签中的内容的开始符  
	- close：设置foreach标签中的内容的结束符
- 批量删除

	```xml
	<!--int deleteMoreByArray(Integer[] eids);-->
	<delete id="deleteMoreByArray">
		delete from t_emp where eid in
		<foreach collection="eids" item="eid" separator="," open="(" close=")">
			#{eid}
		</foreach>
	</delete>
	```
	```java
	@Test
	public void deleteMoreByArray() {
		SqlSession sqlSession = SqlSessionUtils.getSqlSession();
		DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
		int result = mapper.deleteMoreByArray(new Integer[]{6, 7, 8, 9});
		System.out.println(result);
	}
	```
	![](Resources/foreach测试结果1.png)
- 批量添加

	```xml
	<!--int insertMoreByList(@Param("emps") List<Emp> emps);-->
	<insert id="insertMoreByList">
		insert into t_emp values
		<foreach collection="emps" item="emp" separator=",">
			(null,#{emp.empName},#{emp.age},#{emp.sex},#{emp.email},null)
		</foreach>
	</insert>
	```
	```java
	@Test
	public void insertMoreByList() {
		SqlSession sqlSession = SqlSessionUtils.getSqlSession();
		DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
		Emp emp1 = new Emp(null,"a",1,"男","123@321.com",null);
		Emp emp2 = new Emp(null,"b",1,"男","123@321.com",null);
		Emp emp3 = new Emp(null,"c",1,"男","123@321.com",null);
		List<Emp> emps = Arrays.asList(emp1, emp2, emp3);
		int result = mapper.insertMoreByList(emps);
		System.out.println(result);
	}
	```
	![](Resources/foreach测试结果2.png)
## SQL片段
- sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入
- 声明sql片段：`<sql>`标签
```xml
<sql id="empColumns">eid,emp_name,age,sex,email</sql>
```
- 引用sql片段：`<include>`标签
```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
	select <include refid="empColumns"></include> from t_emp
</select>
```
# MyBatis的缓存
## MyBatis的一级缓存
- 一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问  
- 使一级缓存失效的四种情况：  

	1. 不同的SqlSession对应不同的一级缓存  
	2. 同一个SqlSession但是查询条件不同
	3. 同一个SqlSession两次查询期间执行了任何一次增删改操作
	4. 同一个SqlSession两次查询期间手动清空了缓存（clearCache方法）
## MyBatis的二级缓存
- 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取  
- 二级缓存开启的条件

	1. 在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置
	2. 在映射文件中设置标签<cache />
	3. 二级缓存必须在SqlSession关闭或提交之后有效
	4. 查询的数据所转换的实体类类型必须实现序列化的接口
- 使二级缓存失效的情况：两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效
## 二级缓存的相关配置
- 在mapper配置文件中添加的cache标签可以设置一些属性
- eviction属性：缓存回收策略  
	- LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。  
	- FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。  
	- SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。  
	- WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
	- 默认的是 LRU
- flushInterval属性：刷新间隔，单位毫秒
	- 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句（增删改）时刷新
- size属性：引用数目，正整数
	- 代表缓存最多可以存储多少个对象，太大容易导致内存溢出
- readOnly属性：只读，true/false
	- true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。  
	- false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是false
## MyBatis缓存查询的顺序
- 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用  
- 如果二级缓存没有命中，再查询一级缓存  
- 如果一级缓存也没有命中，则查询数据库  
- SqlSession关闭之后，一级缓存中的数据会写入二级缓存
## 整合第三方缓存EHCache（了解）
### 添加依赖
```xml
<!-- Mybatis EHCache整合包 -->
<dependency>
	<groupId>org.mybatis.caches</groupId>
	<artifactId>mybatis-ehcache</artifactId>
	<version>1.2.1</version>
</dependency>
<!-- slf4j日志门面的一个具体实现 -->
<dependency>
	<groupId>ch.qos.logback</groupId>
	<artifactId>logback-classic</artifactId>
	<version>1.2.3</version>
</dependency>
```
### 各个jar包的功能
| jar包名称 | 作用 |
| --- | --- |
| mybatis-ehcache | Mybatis和EHCache的整合包 |
| ehcache | EHCache核心包 |
| slf4j-api | SLF4J日志门面包 |
| logback-classic | 支持SLF4J门面接口的一个具体实现 |

### 创建EHCache的配置文件ehcache.xml
- 名字必须叫`ehcache.xml`
```xml
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\atguigu\ehcache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```
### 设置二级缓存的类型
- 在xxxMapper.xml文件中设置二级缓存类型
```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```
### 加入logback日志
- 存在SLF4J时，作为简易日志的log4j将失效，此时我们需要借助SLF4J的具体实现logback来打印日志。创建logback的配置文件`logback.xml`，名字固定，不可改变
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger] [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.atguigu.crowd.mapper" level="DEBUG"/>
</configuration>
```
### EHCache配置文件说明
| 属性名 | 是否必须 | 作用 |
| --- | --- | --- |
| maxElementsInMemory | 是 | 在内存中缓存的element的最大数目 |
| maxElementsOnDisk | 是 | 在磁盘上缓存的element的最大数目，若是0表示无穷大 |
| eternal | 是 | 设定缓存的elements是否永远不过期。 如果为true，则缓存的数据始终有效， 如果为false那么还要根据timeToIdleSeconds、timeToLiveSeconds判断 |
| overflowToDisk | 是 | 设定当内存缓存溢出的时候是否将过期的element缓存到磁盘上 |
| timeToIdleSeconds | 否 | 当缓存在EhCache中的数据前后两次访问的时间超过timeToIdleSeconds的属性取值时， 这些数据便会删除，默认值是0,也就是可闲置时间无穷大 |
| timeToLiveSeconds | 否 | 缓存element的有效生命期，默认是0.,也就是element存活时间无穷大 |
| diskSpoolBufferSizeMB | 否 | DiskStore(磁盘缓存)的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区 |
| diskPersistent | 否 | 在VM重启的时候是否启用磁盘保存EhCache中的数据，默认是false |
| diskExpiryThreadIntervalSeconds | 否 | 磁盘缓存的清理线程运行间隔，默认是120秒。每个120s， 相应的线程会进行一次EhCache中数据的清理工作 |
| memoryStoreEvictionPolicy | 否 | 当内存缓存达到最大，有新的element加入的时候， 移除缓存中element的策略。 默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出 |
# MyBatis的逆向工程
- 正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的
- 逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源：  
	- Java实体类  
	- Mapper接口  
	- Mapper映射文件
## 创建逆向工程的步骤
### 添加依赖和插件
```xml
<dependencies>
	<!-- MyBatis核心依赖包 -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>3.5.9</version>
	</dependency>
	<!-- junit测试 -->
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.13.2</version>
		<scope>test</scope>
	</dependency>
	<!-- MySQL驱动 -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>8.0.27</version>
	</dependency>
	<!-- log4j日志 -->
	<dependency>
		<groupId>log4j</groupId>
		<artifactId>log4j</artifactId>
		<version>1.2.17</version>
	</dependency>
</dependencies>
<!-- 控制Maven在构建过程中相关配置 -->
<build>
	<!-- 构建过程中用到的插件 -->
	<plugins>
		<!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
		<plugin>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-maven-plugin</artifactId>
			<version>1.3.0</version>
			<!-- 插件的依赖 -->
			<dependencies>
				<!-- 逆向工程的核心依赖 -->
				<dependency>
					<groupId>org.mybatis.generator</groupId>
					<artifactId>mybatis-generator-core</artifactId>
					<version>1.3.2</version>
				</dependency>
				<!-- 数据库连接池 -->
				<dependency>
					<groupId>com.mchange</groupId>
					<artifactId>c3p0</artifactId>
					<version>0.9.2</version>
				</dependency>
				<!-- MySQL驱动 -->
				<dependency>
					<groupId>mysql</groupId>
					<artifactId>mysql-connector-java</artifactId>
					<version>8.0.27</version>
				</dependency>
			</dependencies>
		</plugin>
	</plugins>
</build>
```
### 创建MyBatis的核心配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties"/>
    <typeAliases>
        <package name=""/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <package name=""/>
    </mappers>
</configuration>
```
### 创建逆向工程的配置文件
- 文件名必须是：`generatorConfig.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
    targetRuntime: 执行生成的逆向工程的版本
    MyBatis3Simple: 生成基本的CRUD（清新简洁版）
    MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3Simple">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/mybatis"
                        userId="root"
                        password="123456">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.atguigu.mybatis.pojo" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.atguigu.mybatis.mapper"
                         targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.atguigu.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```
### 执行MBG插件的generate目标
- ![](Resources/执行MBG插件的generate目标.png)
- 如果出现报错：`Exception getting JDBC Driver`，可能是pom.xml中，数据库驱动配置错误
	- dependency中的驱动![](Resources/dependency中的驱动.png)
	- mybatis-generator-maven-plugin插件中的驱动![](Resources/插件中的驱动.png)
	- 两者的驱动版本应该相同
- 执行结果![](Resources/逆向执行结果.png)
## QBC
### 查询
- `selectByExample`：按条件查询，需要传入一个example对象或者null；如果传入一个null，则表示没有条件，也就是查询所有数据
- `example.createCriteria().xxx`：创建条件对象，通过andXXX方法为SQL添加查询添加，每个条件之间是and关系
- `example.or().xxx`：将之前添加的条件通过or拼接其他条件
![](Resources/example的方法.png)
```java
@Test public void testMBG() throws IOException {
	InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
	SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
	SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
	SqlSession sqlSession = sqlSessionFactory.openSession(true);
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	EmpExample example = new EmpExample();
	//名字为张三，且年龄大于等于20
	example.createCriteria().andEmpNameEqualTo("张三").andAgeGreaterThanOrEqualTo(20);
	//或者did不为空
	example.or().andDidIsNotNull();
	List<Emp> emps = mapper.selectByExample(example);
	emps.forEach(System.out::println);
}
```
![](Resources/example测试结果.png)
### 增改
- `updateByPrimaryKey`：通过主键进行数据修改，如果某一个值为null，也会将对应的字段改为null
	- `mapper.updateByPrimaryKey(new Emp(1,"admin",22,null,"456@qq.com",3));`
	- ![](Resources/增删改测试结果1.png)
- `updateByPrimaryKeySelective()`：通过主键进行选择性数据修改，如果某个值为null，则不修改这个字段
	- `mapper.updateByPrimaryKeySelective(new Emp(2,"admin2",22,null,"456@qq.com",3));`
	- ![](Resources/增删改测试结果2.png)
# 分页插件
## 分页插件使用步骤
### 添加依赖
```xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>5.2.0</version>
</dependency>
```
### 配置分页插件
- 在MyBatis的核心配置文件（mybatis-config.xml）中配置插件
- ![](Resources/配置分页插件.png)
```xml
<plugins>
	<!--设置分页插件-->
	<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```
## 分页插件的使用
### 开启分页功能
- 在查询功能之前使用`PageHelper.startPage(int pageNum, int pageSize)`开启分页功能
	- pageNum：当前页的页码  
	- pageSize：每页显示的条数
```java
@Test
public void testPageHelper() throws IOException {
	InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
	SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
	SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
	SqlSession sqlSession = sqlSessionFactory.openSession(true);
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	//访问第一页，每页四条数据
	PageHelper.startPage(1,4);
	List<Emp> emps = mapper.selectByExample(null);
	emps.forEach(System.out::println);
}
```

![](Resources/分页测试结果.png)
### 分页相关数据
#### 方法一：直接输出
```java
@Test
public void testPageHelper() throws IOException {
	InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
	SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
	SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
	SqlSession sqlSession = sqlSessionFactory.openSession(true);
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	//访问第一页，每页四条数据
	Page<Object> page = PageHelper.startPage(1, 4);
	List<Emp> emps = mapper.selectByExample(null);
	//在查询到List集合后，打印分页数据
	System.out.println(page);
}
```
- 分页相关数据：

	```
	Page{count=true, pageNum=1, pageSize=4, startRow=0, endRow=4, total=8, pages=2, reasonable=false, pageSizeZero=false}[Emp{eid=1, empName='admin', age=22, sex='男', email='456@qq.com', did=3}, Emp{eid=2, empName='admin2', age=22, sex='男', email='456@qq.com', did=3}, Emp{eid=3, empName='王五', age=12, sex='女', email='123@qq.com', did=3}, Emp{eid=4, empName='赵六', age=32, sex='男', email='123@qq.com', did=1}]
	```
#### 方法二使用PageInfo
- 在查询获取list集合之后，使用`PageInfo<T> pageInfo = new PageInfo<>(List<T> list, intnavigatePages)`获取分页相关数据
	- list：分页之后的数据  
	- navigatePages：导航分页的页码数
```java
@Test
public void testPageHelper() throws IOException {
	InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
	SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
	SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
	SqlSession sqlSession = sqlSessionFactory.openSession(true);
	EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
	PageHelper.startPage(1, 4);
	List<Emp> emps = mapper.selectByExample(null);
	PageInfo<Emp> page = new PageInfo<>(emps,5);
	System.out.println(page);
}
```
- 分页相关数据：

	```
	PageInfo{
	pageNum=1, pageSize=4, size=4, startRow=1, endRow=4, total=8, pages=2, 
	list=Page{count=true, pageNum=1, pageSize=4, startRow=0, endRow=4, total=8, pages=2, reasonable=false, pageSizeZero=false}[Emp{eid=1, empName='admin', age=22, sex='男', email='456@qq.com', did=3}, Emp{eid=2, empName='admin2', age=22, sex='男', email='456@qq.com', did=3}, Emp{eid=3, empName='王五', age=12, sex='女', email='123@qq.com', did=3}, Emp{eid=4, empName='赵六', age=32, sex='男', email='123@qq.com', did=1}], 
	prePage=0, nextPage=2, isFirstPage=true, isLastPage=false, hasPreviousPage=false, hasNextPage=true, navigatePages=5, navigateFirstPage=1, navigateLastPage=2, navigatepageNums=[1, 2]}
	```
- 其中list中的数据等同于方法一中直接输出的page数据
#### 常用数据：
- pageNum：当前页的页码  
- pageSize：每页显示的条数  
- size：当前页显示的真实条数  
- total：总记录数  
- pages：总页数  
- prePage：上一页的页码  
- nextPage：下一页的页码
- isFirstPage/isLastPage：是否为第一页/最后一页  
- hasPreviousPage/hasNextPage：是否存在上一页/下一页  
- navigatePages：导航分页的页码数  
- navigatepageNums：导航分页的页码，\[1,2,3,4,5]

# MyBatis-Plus

## 1.简介

[MyBatis-Plus (opens new window)](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis (opens new window)](https://www.mybatis.org/mybatis-3/)的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

> 我们的愿景是成为 MyBatis 最好的搭档，就像 [魂斗罗](https://baomidou.com/img/contra.jpg) 中的 1P、2P，基友搭配，效率翻倍。

![img](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/relationship-with-mybatis.png)

## 2.特性

-   **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
    
-   **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
    
-   **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
    
-   **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
    
-   **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
    
-   **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
    
-   **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
    
-   **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
    
-   **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
    
-   **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
    
-   **内置性能分析插件**：可输出 SQL 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
    
-   **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作
    

## 3.支持数据库

> 任何能使用 `MyBatis` 进行 CRUD, 并且支持标准 SQL 的数据库，具体支持情况如下，如果不在下列表查看分页部分教程 PR 您的支持。

-   MySQL，Oracle，DB2，H2，HSQL，SQLite，PostgreSQL，SQLServer，Phoenix，Gauss ，ClickHouse，Sybase，OceanBase，Firebird，Cubrid，Goldilocks，csiidb
    
-   达梦数据库，虚谷数据库，人大金仓数据库，南大通用(华库)数据库，南大通用数据库，神通数据库，瀚高数据库
    

## 4.框架结构

![framework](https://baomidou.com/img/mybatis-plus-framework.jpg)

## 5.官方地址

> **官方网站：**[https://baomidou.com/](https://baomidou.com/)
> 
> **官方文档：**[https://baomidou.com/pages/24112f/](https://baomidou.com/pages/24112f/)

# 二、入门案例

## 1.开发环境

-   **IDE：IDEA 2019.3.5**
    
-   **JDK：JDK8+**
    
-   **构建工具：Maven 3.5.4**
    
-   **MySQL：MySQL 8.0.24**
    
-   **Navicat：Navicat Premium 15**
    
-   **Spring Boot：2.6.7**
    
-   **MyBatis-Plus：3.5.1**
    

## 2.建库建表

-   **打开Navicat运行以下SQL脚本进行建库建表**
    
    CREATE DATABASE `mybatis_plus` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;   
    use `mybatis_plus`;   
    CREATE TABLE `user` (   
     `id` bigint(20) NOT NULL COMMENT '主键ID',   
     `name` varchar(30) DEFAULT NULL COMMENT '姓名',   
     `age` int(11) DEFAULT NULL COMMENT '年龄',   
     `email` varchar(50) DEFAULT NULL COMMENT '邮箱',   
     PRIMARY KEY (`id`)   
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    
-   **插入几条测试数据**
    
    INSERT INTO user (id, name, age, email) VALUES   
    (1, 'Jone', 18, 'test1@baomidou.com'),   
    (2, 'Jack', 20, 'test2@baomidou.com'),   
    (3, 'Tom', 28, 'test3@baomidou.com'),   
    (4, 'Sandy', 21, 'test4@baomidou.com'),   
    (5, 'Billie', 24, 'test5@baomidou.com');
    

## 3.创建工程

-   **使用`Spring Initializer`快速初始化一个 Spring Boot 工程**
    
    ![image-20220519140839640](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519140839640.png)
    
    ![image-20220519141335981](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519141335981.png)
    
    ![image-20220519141737405](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519141737405.png)
    
    ![image-20220519141849937](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519141849937.png)
    
-   **引入`MyBatis-Plus`的依赖**
    
    <dependency>  
     <groupId>com.baomidou</groupId>  
     <artifactId>mybatis-plus-boot-starter</artifactId>  
     <version>3.5.1</version>  
    </dependency>
    
-   **安装`Lombok`插件**
    
    ![image-20220519143257305](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519143257305.png)
    

## 4.配置编码

-   **配置`application.yml`文件**
    
    #配置端口  
    server:  
     port: 80  
    ​  
    spring:  
     #配置数据源  
     datasource:  
     #配置数据源类型  
     type: com.zaxxer.hikari.HikariDataSource  
     #配置连接数据库的信息  
     driver-class-name: com.mysql.cj.jdbc.Driver  
     url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false  
     username: {username}  
     password: {password}  
    ​  
    #MyBatis-Plus相关配置  
    mybatis-plus:  
     configuration:  
     #配置日志  
     log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    
-   **在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹**
    
    @SpringBootApplication  
    @MapperScan("指定Mapper接口所在的包")  
    public class MybatisPlusDemoApplication {  
     public static void main(String[] args) {  
     SpringApplication.run(MybatisPlusDemoApplication.class, args);  
     }  
    }
    
-   **编写实体类 `User.java`（此处使用了 Lombok 简化代码）**
    
    @Data  
    public class User {  
     private Long id;  
     private String name;  
     private Integer age;  
     private String email;  
    }
    
-   **编写 Mapper 包下的 `UserMapper`接口**
    
    public interface UserMapper extends BaseMapper<User> {}
    

## 5.测试查询

-   **编写一个测试类`MyBatisPlusTest.java`**
    
    @SpringBootTest  
    public class MyBatisPlusTest {  
     @Resource  
     private UserMapper userMapper;  
    ​  
     /**  
     * 测试查询所有数据  
     */  
     @Test  
     void testSelectList(){  
     //通过条件构造器查询一个list集合，若没有条件，则可以设置null为参数  
     List<User> users = userMapper.selectList(null);  
     users.forEach(System.out::println);  
     }  
    }
    
-   **控制台打印查询结果**
    
    ![image-20220519150454211](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220519150454211.png)
    

# 三、增删改查

## 1.BaseMapper<T>

> 说明:
> 
> -   通用 CRUD 封装BaseMapper 接口，为 `Mybatis-Plus` 启动时自动解析实体表关系映射转换为 `Mybatis` 内部对象注入容器
>     
> -   泛型 `T` 为任意实体对象
>     
> -   参数 `Serializable` 为任意类型主键 `Mybatis-Plus` 不推荐使用复合主键约定每一张表都有自己的唯一 `id` 主键
>     
> -   对象 `Wrapper` 为条件构造器
>     

MyBatis-Plus中的基本CRUD在内置的BaseMapper中都已得到了实现，因此我们继承该接口以后可以直接使用。

本次演示的CRUD操作不包含参数带有条件构造器的方法，关于条件构造器将单独在一个章节进行演示。

----------

> **BaseMapper中提供的CRUD方法：**

-   **增加：Insert**
    
    // 插入一条记录  
    int insert(T entity);
    
-   **删除：Delete**
    
    // 根据 entity 条件，删除记录  
    int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);  
    // 删除（根据ID 批量删除）  
    int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);  
    // 根据 ID 删除  
    int deleteById(Serializable id);  
    // 根据 columnMap 条件，删除记录  
    int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
    
-   **修改：Update**
    
    // 根据 whereWrapper 条件，更新记录  
    int update(@Param(Constants.ENTITY) T updateEntity, @Param(Constants.WRAPPER) Wrapper<T> whereWrapper);  
    // 根据 ID 修改  
    int updateById(@Param(Constants.ENTITY) T entity);
    
-   **查询：Selete**
    
    // 根据 ID 查询  
    T selectById(Serializable id);  
    // 根据 entity 条件，查询一条记录  
    T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
      
    // 查询（根据ID 批量查询）  
    List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);  
    // 根据 entity 条件，查询全部记录  
    List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
    // 查询（根据 columnMap 条件）  
    List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);  
    // 根据 Wrapper 条件，查询全部记录  
    List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
    // 根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值  
    List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
      
    // 根据 entity 条件，查询全部记录（并翻页）  
    IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
    // 根据 Wrapper 条件，查询全部记录（并翻页）  
    IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);  
    // 根据 Wrapper 条件，查询总记录数  
    Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
    

## 2.调用Mapper层实现CRUD

### 2.1  插入

----------

> **最终执行的结果，所获取的id为1527206783590903810**
> 
> **这是因为MyBatis-Plus在实现插入数据时，会默认基于雪花算法的策略生成id**

/**  
  * 测试插入一条数据  
  * MyBatis-Plus在实现插入数据时，会默认基于雪花算法的策略生成id  
  */  
@Test  
public void testInsert(){  
    User user = new User();  
    user.setName("Vz");  
    user.setAge(21);  
    user.setEmail("vz@oz6.cn");  
    int result = userMapper.insert(user);  
    System.out.println(result > 0 ? "添加成功！" : "添加失败！");  
    System.out.println("受影响的行数为：" + result);  
    //1527206783590903810（当前 id 为雪花算法自动生成的id）  
    System.out.println("id自动获取" + user.getId());  
}

### 2.2  删除

----------

#### a、根据ID删除数据

> **调用方法：int deleteById(Serializable id);**

/**  
  * 测试根据id删除一条数据  
  */  
@Test  
public void testDeleteById(){  
    int result = userMapper.deleteById(1527206783590903810L);  
    System.out.println(result > 0 ? "删除成功！" : "删除失败！");  
    System.out.println("受影响的行数为：" + result);  
}

#### b、根据ID批量删除数据

> **调用方法：int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);**

/**  
  * 测试通过id批量删除数据  
  */  
@Test  
public void testDeleteBatchIds(){  
    List<Long> ids = Arrays.asList(6L,7L,8L);  
    int result = userMapper.deleteBatchIds(ids);  
    System.out.println(result > 0 ? "删除成功！" : "删除失败！");  
    System.out.println("受影响的行数为：" + result);  
}

#### c、根据Map条件删除数据

> **调用方法：int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);**

/**  
   * 测试根据Map集合中所设置的条件删除数据  
   */  
@Test  
public void testDeleteByMap(){  
    //当前演示为根据name和age删除数据  
    //执行SQL为：DELETE FROM user WHERE name = ? AND age = ?  
    Map<String,Object> map = new HashMap<>();  
    map.put("name","Vz");  
    map.put("age",21);  
    int result = userMapper.deleteByMap(map);  
    System.out.println(result > 0 ? "删除成功！" : "删除失败！");  
    System.out.println("受影响的行数为：" + result);  
}

### 2.3  修改

> **调用方法：int updateById(@Param(Constants.ENTITY) T entity);**

/**  
  * 测试根据id修改用户信息  
  */  
@Test  
public void testUpdateById(){  
    //执行SQL为： UPDATE user SET name=?, age=?, email=? WHERE id=?  
    User user = new User();  
    user.setId(6L);  
    user.setName("VzUpdate");  
    user.setAge(18);  
    user.setEmail("Vz@sina.com");  
    int result = userMapper.updateById(user);  
    System.out.println(result > 0 ? "修改成功！" : "修改失败！");  
    System.out.println("受影响的行数为：" + result);  
}

### 2.4  查询

----------

#### a、根据ID查询用户信息

> **调用方法：T selectById(Serializable id);**

/**  
  * 测试根据id查询用户数据  
  */  
@Test  
public void testSelectById(){  
    User user = userMapper.selectById(1L);  
    System.out.println(user);  
}

#### b、根据多个ID查询多个用户信息

> **调用方法：List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);**

/**  
  * 根据多个id查询用户数据  
  */  
@Test  
public void testSelectBatchIds(){  
    //执行SQL为：SELECT id,name,age,email FROM user WHERE id IN ( ? , ? , ? )  
    List<Long> ids = Arrays.asList(1L,2L,3L);  
    List<User> users = userMapper.selectBatchIds(ids);  
    users.forEach(System.out::println);  
}

#### c、根据Map条件查询用户信息

> **调用方法：List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);**

/**  
  * 根据Map所设置的条件查询用户  
  */  
@Test  
public void testSelectByMap(){  
    //执行SQL为：SELECT id,name,age,email FROM user WHERE age = ?  
    Map<String,Object> map = new HashMap<>();  
    map.put("age",18);  
    List<User> users = userMapper.selectByMap(map);  
    users.forEach(System.out::println);  
}

#### d、查询所有用户信息

> **调用方法：List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);**

/**  
  * 测试查询所有数据  
  */  
@Test  
void testSelectList(){  
    List<User> users = userMapper.selectList(null);  
    users.forEach(System.out::println);  
}

## 3.通用Service

> 说明:
> 
> -   通用 Service CRUD 封装`IService`接口，进一步封装 CRUD 采用 `get 查询单行`  `remove 删除`  `list 查询集合`  `page 分页` 前缀命名方式区分 `Mapper` 层避免混淆，
>     
> -   泛型 `T` 为任意实体对象
>     
> -   建议如果存在自定义通用 Service 方法的可能，请创建自己的 `IBaseService` 继承 `Mybatis-Plus` 提供的基类
>     
> -   对象 `Wrapper` 为 条件构造器
>     

MyBatis-Plus中有一个接口 **`IService`**和其实现类 **`ServiceImpl`**，封装了常见的业务层逻辑，详情查看源码IService和ServiceImpl

因此我们在使用的时候仅需在自己定义的**`Service`**接口中继承**`IService`**接口，在自己的实现类中实现自己的Service并继承**`ServiceImpl`**即可

----------

> **IService中的CRUD方法**

-   **增加：Save、SaveOrUpdate**
    
    // 插入一条记录（选择字段，策略插入）  
    boolean save(T entity);  
    // 插入（批量）  
    boolean saveBatch(Collection<T> entityList);  
    // 插入（批量）  
    boolean saveBatch(Collection<T> entityList, int batchSize);  
      
    // TableId 注解存在更新记录，否插入一条记录  
    boolean saveOrUpdate(T entity);  
    // 根据updateWrapper尝试更新，否继续执行saveOrUpdate(T)方法  
    boolean saveOrUpdate(T entity, Wrapper<T> updateWrapper);  
    // 批量修改插入  
    boolean saveOrUpdateBatch(Collection<T> entityList);  
    // 批量修改插入  
    boolean saveOrUpdateBatch(Collection<T> entityList, int batchSize);
    
-   **删除：Remove**
    
    // 根据 entity 条件，删除记录  
    boolean remove(Wrapper<T> queryWrapper);  
    // 根据 ID 删除  
    boolean removeById(Serializable id);  
    // 根据 columnMap 条件，删除记录  
    boolean removeByMap(Map<String, Object> columnMap);  
    // 删除（根据ID 批量删除）  
    boolean removeByIds(Collection<? extends Serializable> idList);
    
-   **修改：Update**
    
    // 根据 UpdateWrapper 条件，更新记录 需要设置sqlset  
    boolean update(Wrapper<T> updateWrapper);  
    // 根据 whereWrapper 条件，更新记录  
    boolean update(T updateEntity, Wrapper<T> whereWrapper);  
    // 根据 ID 选择修改  
    boolean updateById(T entity);  
    // 根据ID 批量更新  
    boolean updateBatchById(Collection<T> entityList);  
    // 根据ID 批量更新  
    boolean updateBatchById(Collection<T> entityList, int batchSize);
    
-   **查询：Get、List、Count**
    
    // 根据 ID 查询  
    T getById(Serializable id);  
    // 根据 Wrapper，查询一条记录。结果集，如果是多个会抛出异常，随机取一条加上限制条件 wrapper.last("LIMIT 1")  
    T getOne(Wrapper<T> queryWrapper);  
    // 根据 Wrapper，查询一条记录  
    T getOne(Wrapper<T> queryWrapper, boolean throwEx);  
    // 根据 Wrapper，查询一条记录  
    Map<String, Object> getMap(Wrapper<T> queryWrapper);  
    // 根据 Wrapper，查询一条记录  
    <V> V getObj(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);  
      
      
    // 查询所有  
    List<T> list();  
    // 查询列表  
    List<T> list(Wrapper<T> queryWrapper);  
    // 查询（根据ID 批量查询）  
    Collection<T> listByIds(Collection<? extends Serializable> idList);  
    // 查询（根据 columnMap 条件）  
    Collection<T> listByMap(Map<String, Object> columnMap);  
    // 查询所有列表  
    List<Map<String, Object>> listMaps();  
    // 查询列表  
    List<Map<String, Object>> listMaps(Wrapper<T> queryWrapper);  
    // 查询全部记录  
    List<Object> listObjs();  
    // 查询全部记录  
    <V> List<V> listObjs(Function<? super Object, V> mapper);  
    // 根据 Wrapper 条件，查询全部记录  
    List<Object> listObjs(Wrapper<T> queryWrapper);  
    // 根据 Wrapper 条件，查询全部记录  
    <V> List<V> listObjs(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);  
      
    // 查询总记录数  
    int count();  
    // 根据 Wrapper 条件，查询总记录数  
    int count(Wrapper<T> queryWrapper);
    
-   **分页：Page**
    
    // 根据 ID 查询  
    T getById(Serializable id);  
    // 根据 Wrapper，查询一条记录。结果集，如果是多个会抛出异常，随机取一条加上限制条件 wrapper.last("LIMIT 1")  
    T getOne(Wrapper<T> queryWrapper);  
    // 根据 Wrapper，查询一条记录  
    T getOne(Wrapper<T> queryWrapper, boolean throwEx);  
    // 根据 Wrapper，查询一条记录  
    Map<String, Object> getMap(Wrapper<T> queryWrapper);  
    // 根据 Wrapper，查询一条记录  
    <V> V getObj(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);
    

## 4.调用Service层操作数据

> 我们在自己的Service接口中通过继承MyBatis-Plus提供的IService接口，不仅可以获得其提供的CRUD方法，而且还可以使用自身定义的方法。

-   **创建`UserService`并继承`IService`**
    
    /**  
      * UserService继承IService模板提供的基础功能   
      */  
    public interface UserService extends IService<User> {}
    
-   **创建`UserService`的实现类并继承`ServiceImpl`**
    
    /**  
      * ServiceImpl实现了IService，提供了IService中基础功能的实现   
      * 若ServiceImpl无法满足业务需求，则可以使用自定的UserService定义方法，并在实现类中实现  
      */  
    @Service  
    public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService{}
    
-   **测试查询记录数**
    
    > **调用方法：int count();**
    
    @Test  
    public void testGetCount(){  
        //查询总记录数  
        //执行的SQL为：SELECT COUNT( * ) FROM user  
        long count = userService.count();  
        System.out.println("总记录数：" + count);  
    }
    
-   **测试批量插入数据**
    
    > **调用方法：boolean saveBatch(Collection<T> entityList);**
    
    @Test  
    public void test(){  
        List<User> list = new ArrayList<>();  
        for (int i = 1; i <= 10; i++) {  
            User user = new User();  
            user.setName("Vz"+i);  
            user.setAge(20+i);  
            list.add(user);  
        }  
        boolean b = userService.saveBatch(list);  
        System.out.println(b ? "添加成功！" : "添加失败！");  
    }
    

# 四、常用注解

> MyBatis-Plus提供的注解可以帮我们解决一些数据库与实体之间相互映射的问题。

## 1.@TableName

> 经过以上的测试，在使用MyBatis-Plus实现基本的CRUD时，我们并没有指定要操作的表，只是在Mapper接口继承BaseMapper时，设置了泛型User，而操作的表为user表，由此得出结论，MyBatis-Plus在确定操作的表时，由BaseMapper的泛型决定，即实体类型决定，且默认操作的表名和实体类型的类名一致。

### 1.1  引出问题

----------

> **若实体类类型的类名和要操作的表的表名不一致，会出现什么问题？**

-   我们将表`user`更名为`t_user`，测试查询功能
    
    ![image-20220520093844842](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520093844842.png)
    
-   程序抛出异常，**Table 'mybatis_plus.user' doesn't exist**，因为现在的表名为`t_user`，而默认操作的表名和实体类型的类名一致，即`user`表
    
    ![image-20220520094126411](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520094126411.png)
    

### 1.2  解决问题

----------

#### a、使用注解解决问题

> **在实体类类型上添加`@TableName("t_user")`，标识实体类对应的表，即可成功执行SQL语句**

@Data  
@TableName("t_user")  
public class User {  
    private Long id;  
    private String name;  
    private Integer age;  
    private String email;  
}

#### b、使用全局配置解决问题

> **在开发的过程中，我们经常遇到以上的问题，即实体类所对应的表都有固定的前缀，例如 `t_` 或 `tbl_` 此时，可以使用MyBatis-Plus提供的全局配置，为实体类所对应的表名设置默认的前缀，那么就不需要在每个实体类上通过@TableName标识实体类对应的表**

mybatis-plus:  
  global-config:  
    db-config:  
      # 设置实体类所对应的表的统一前缀  
      table-prefix: t_

## 2.@TableId

> **经过以上的测试，MyBatis-Plus在实现CRUD时，会默认将id作为主键列，并在插入数据时，默认基于雪花算法的策略生成id**

### 2.1  引出问题

----------

> **若实体类和表中表示主键的不是id，而是其他字段，例如uid，MyBatis-Plus会自动识别uid为主键列吗？**

-   我们实体类中的属性`id`改为`uid`，将表中的字段`id`也改为`uid`，测试添加功能
    
    ![image-20220520100939157](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520100939157.png)
    
    ![image-20220520100715109](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520100715109.png)
    
-   程序抛出异常，**Field 'uid' doesn't have a default value**，说明MyBatis-Plus没有将`uid`作为主键赋值
    
    ![image-20220520101317761](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520101317761.png)
    

### 2.2  解决问题

----------

> **在实体类中uid属性上通过`@TableId`将其标识为主键，即可成功执行SQL语句**

@Date  
public class User {  
    @TableId  
    private Long uid;  
    private String name;  
    private Integer age;  
    private String email;  
}

### 2.3  @TableId的value属性

----------

> 若实体类中主键对应的属性为id，而表中表示主键的字段为uid，此时若只在属性id上添加注解@TableId，则抛出异常**Unknown column 'id' in 'field list'**，即MyBatis-Plus仍然会将id作为表的主键操作，而表中表示主键的是字段uid此时需要通过@TableId注解的value属性，指定表中的主键字段，`@TableId("uid")`或`@TableId(value="uid")`

![image-20220520103030977](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520103030977.png)

### 2.4  @TableId的type属性

----------

> **type属性用来定义主键策略：默认雪花算法**

**常用的主键策略：**

值

描述

IdType.ASSIGN_ID（默认）

基于雪花算法的策略生成数据id，与数据库id是否设置自增无关

IdType.AUTO

使用数据库的自增策略，注意，该类型请确保数据库设置了id自增，

**配置全局主键策略：**

#MyBatis-Plus相关配置  
mybatis-plus:  
  configuration:  
    #配置日志  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
  global-config:  
    db-config:  
      #配置mp的主键策略为自增  
      id-type: auto  
      # 设置实体类所对应的表的统一前缀  
      table-prefix: t_

## 3.@TbaleField

> 经过以上的测试，我们可以发现，MyBatis-Plus在执行SQL语句时，要保证实体类中的属性名和表中的字段名一致
> 
> 如果实体类中的属性名和字段名不一致的情况，会出现什么问题呢？

### 3.1  情况一

----------

若实体类中的属性使用的是驼峰命名风格，而表中的字段使用的是下划线命名风格

例如实体类属性`userName`，表中字段`user_name`

此时MyBatis-Plus会自动将下划线命名风格转化为驼峰命名风格

相当于在MyBatis中配置

### 3.2  情况二

----------

> 若实体类中的属性和表中的字段不满足情况1
> 
> 例如实体类属性`name`，表中字段`username`
> 
> 此时需要在实体类属性上使用`@TableField("username")`设置属性所对应的字段名

public class User {  
    @TableId("uid")  
    private Long id;  
    @TableField("username")  
    private String name;  
    private Integer age;  
    private String email;  
}

## 4.@TableLogic

### 4.1  逻辑删除

----------

> 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除的数据
> 
> 逻辑删除：假删除，将对应数据中代表是否被删除字段的状态修改为“被删除状态”，之后在数据库中仍旧能看到此条数据记录
> 
> 使用场景：可以进行数据恢复

### 4.2  实现逻辑删除

----------

-   **数据库中创建逻辑删除状态列，设置默认值为0**
    
    ![image-20220520134529809](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520134529809.png)
    
-   **实体类中添加逻辑删除属性**
    
    ![image-20220520134636112](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520134636112.png)
    
-   **测试删除功能，真正执行的是修改**
    
    public void testDeleteById(){  
        int result = userMapper.deleteById(1527472864163348482L);  
        System.out.println(result > 0 ? "删除成功！" : "删除失败！");  
        System.out.println("受影响的行数为：" + result);  
    }
    
    ![image-20220520135637388](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520135637388.png)
    
-   **此时执行查询方法，查询的结果为自动添加条件`is_deleted=0`**
    
    ![image-20220520140036445](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220520140036445.png)
    

# 五、条件构造器

## 1.Wrapper介绍

![image-20220521092812125](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220521092812125.png)

-   `Wrapper` ： 条件构造抽象类，最顶端父类
    
    -   `AbstractWrapper`： 用于查询条件封装，生成 sql 的 where 条件
        
        -   `QueryWrapper`： 查询条件封装
            
        -   `UpdateWrapper`： Update 条件封装
            
        -   `AbstractLambdaWrapper`： 使用Lambda 语法
            
            -   `LambdaQueryWrapper`：用于Lambda语法使用的查询Wrapper
                
            -   `LambdaUpdateWrapper`： Lambda 更新封装Wrapper
                

## 2.QueryWrapper

-   **组装查询条件**
    
    > **执行SQL：**SELECT uid AS id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 AND (username LIKE ? AND age BETWEEN ? AND ? AND email IS NOT NULL)
    
    public void test01(){  
        //查询用户名包含a，年龄在20到30之间，邮箱信息不为null的用户信息  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.like("username","a").between("age",20,30).isNotNull("email");  
        List<User> users = userMapper.selectList(queryWrapper);  
        users.forEach(System.out::println);  
    }
    
-   **组装排序条件**
    
    > **执行SQL：**SELECT uid AS id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 ORDER BY age DESC,id ASC
    
    public void test02(){  
        //查询用户信息，按照年龄的降序排序，若年龄相同，则按照id升序排序  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.orderByDesc("age").orderByAsc("id");  
        List<User> users = userMapper.selectList(queryWrapper);  
        users.forEach(System.out::println);  
    }
    
-   **组装删除条件**
    
    > **执行SQL：**UPDATE t_user SET is_deleted=1 WHERE is_deleted=0 AND (email IS NULL)
    
    public void test03(){  
        //删除邮箱地址为null的用户信息  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.isNull("email");  
        int result = userMapper.delete(queryWrapper);  
        System.out.println(result > 0 ? "删除成功！" : "删除失败！");  
        System.out.println("受影响的行数为：" + result);  
    }
    
-   **条件的优先级**
    
    > **执行SQL：**UPDATE t_user SET user_name=?, email=? WHERE is_deleted=0 AND (age > ? AND user_name LIKE ? OR email IS NULL)
    
    public void test04(){  
        //将（年龄大于20并且用户名中包含有a）或邮箱为null的用户信息修改  
        UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();  
        updateWrapper.gt("age",20).like("username","a").or().isNull("email");  
        User user = new User();  
        user.setName("Oz");  
        user.setEmail("test@oz6.com");  
      
        int result = userMapper.update(user, updateWrapper);  
        System.out.println(result > 0 ? "修改成功！" : "修改失败！");  
        System.out.println("受影响的行数为：" + result);  
    }
    
    > **执行SQL：**UPDATE t_user SET username=?, email=? WHERE is_deleted=0 AND (username LIKE ? AND (age > ? OR email IS NULL))
    
    public void test05(){  
        //将用户名中包含有a并且（年龄大于20或邮箱为null）的用户信息修改  
        UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();  
        updateWrapper.like("username","a").and(i->i.gt("age",20).or().isNull("email"));  
        User user = new User();  
        user.setName("Vz7797");  
        user.setEmail("test@ss8o.com");  
      
        int result = userMapper.update(user, updateWrapper);  
        System.out.println(result > 0 ? "修改成功！" : "修改失败！");  
        System.out.println("受影响的行数为：" + result);  
    }
    
-   **组装select子句**
    
    > **执行SQL：**SELECT username,age,email FROM t_user WHERE is_deleted=0
    
    public void test06(){  
        //查询用户的用户名、年龄、邮箱信息  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.select("username","age","email");  
        List<Map<String, Object>> maps = userMapper.selectMaps(queryWrapper);  
        maps.forEach(System.out::println);  
    }
    
-   **实现子查询**
    
    > **执行SQL：**SELECT uid AS id,user_name AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 AND (uid IN (select uid from t_user where uid <= 100))
    
    public void test07(){  
        //查询id小于等于100的用户信息  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.inSql("uid", "select uid from t_user where uid <= 100");  
        List<User> list = userMapper.selectList(queryWrapper);  
        list.forEach(System.out::println);  
    }
    

## 3.UpdateWrapper

> UpdateWrapper不仅拥有QueryWrapper的组装条件功能，还提供了set方法进行修改对应条件的数据库信息

public void test08(){  
    //将用户名中包含有a并且（年龄大于20或邮箱为null）的用户信息修改  
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();  
    updateWrapper.like("username","a").and( i -> i.gt("age",20).or().isNull("email")).set("email","svip@qq.com");  
    int result = userMapper.update(null, updateWrapper);  
    System.out.println(result > 0 ? "修改成功！" : "修改失败！");  
    System.out.println("受影响的行数为：" + result);  
}

## 4.condition

> 在真正开发的过程中，组装条件是常见的功能，而这些条件数据来源于用户输入，是可选的，因此我们在组装这些条件时，必须先判断用户是否选择了这些条件，若选择则需要组装该条件，若没有选择则一定不能组装，以免影响SQL执行的结果

-   **思路一**
    
    > **执行SQL：**SELECT uid AS id,user_name AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 AND (user_name LIKE ? AND age <= ?)
    
     public void test09(){  
         String username = "a";  
         Integer ageBegin = null;  
         Integer ageEnd = 30;  
         QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
         if(StringUtils.isNotBlank(username)){  
             //isNotBlank判断某个字符创是否不为空字符串、不为null、不为空白符  
             queryWrapper.like("user_name", username);  
         }  
         if(ageBegin != null){  
             queryWrapper.ge("age", ageBegin);  
         }  
         if(ageEnd != null){  
             queryWrapper.le("age", ageEnd);  
         }  
         List<User> list = userMapper.selectList(queryWrapper);  
         list.forEach(System.out::println);  
     }
    
-   **思路二**
    
    > 上面的实现方案没有问题，但是代码比较复杂，我们可以使用带condition参数的重载方法构建查询条件，简化代码的编写
    
    public void test10(){  
        String username = "a";  
        Integer ageBegin = null;  
        Integer ageEnd = 30;  
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();  
        queryWrapper.like(StringUtils.isNotBlank(username), "user_name", username)  
            .ge(ageBegin != null, "age", ageBegin)  
            .le(ageEnd != null, "age", ageEnd);  
        List<User> list = userMapper.selectList(queryWrapper);  
        list.forEach(System.out::println);  
    }
    

## 5.LambdaQueryWrapper

> 功能等同于QueryWrapper，提供了Lambda表达式的语法可以避免填错列名。

public void test11(){  
    String username = "a";  
    Integer ageBegin = null;  
    Integer ageEnd = 30;  
    LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();  
    queryWrapper.like(StringUtils.isNotBlank(username), User::getName, username)  
        .ge(ageBegin != null, User::getAge, ageBegin)  
        .le(ageEnd != null, User::getAge, ageEnd);  
    List<User> list = userMapper.selectList(queryWrapper);  
    list.forEach(System.out::println);  
}

## 6.LambdaUpdateWrapper

> 功能等同于UpdateWrapper，提供了Lambda表达式的语法可以避免填错列名。

public void test12(){  
    //将用户名中包含有a并且（年龄大于20或邮箱为null）的用户信息修改  
    LambdaUpdateWrapper<User> updateWrapper = new LambdaUpdateWrapper<>();  
    updateWrapper.like(User::getName, "a")  
        .and(i -> i.gt(User::getAge, 20).or().isNull(User::getEmail));  
    updateWrapper.set(User::getName, "小黑").set(User::getEmail,"abc@atguigu.com");  
    int result = userMapper.update(null, updateWrapper);  
    System.out.println("result："+result);  
}

# 六、常用插件

## 1.分页插件

> MyBatis Plus自带分页插件，只要简单的配置即可实现分页功能

-   **添加配置类`MyBatisPlusConfig`**
    
    @Configuration  
    @MapperScan("com.atguigu.mybatisplus.mapper")  
    public class MyBatisPlusConfig {  
        @Bean  
        public MybatisPlusInterceptor mybatisPlusInterceptor(){  
            MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();  
            //添加分页插件  
            interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));  
            return interceptor;  
        }  
    }
    
-   **编写测试方法**
    
    @Test  
    public void testPage(){  
        //new Page()中的两个参数分别是当前页码，每页显示数量  
        Page<User> page = userMapper.selectPage(new Page<>(1, 2), null);  
        List<User> users = page.getRecords();  
        users.forEach(System.out::println);  
    }
    

## 2.自定义分页

> 上面调用的是MyBatis-Plus提供的带有分页的方法，那么我们自己定义的方法如何实现分页呢？

-   **在`UserMapper`接口中定义一个方法**
    
    /**  
      * 根据年龄查询用户列表，分页显示   
      * @param page 分页对象,xml中可以从里面进行取值,传递参数 Page 即自动分页,必须放在第一位   
      * @param age 年龄   
      * @return   
      */  
    Page<User> selectPageVo(@Param("page") Page<User> page,@Param("age") Integer age);
    
-   **在`UserMapper.xml`中编写SQL实现该方法**
    
    <select id="selectPageVo" resultType="User">  
        select id,username as name,age,email from t_user where age > #{age}  
    </select>
    
-   **编写测试方法**
    
    @Test  
    public void testPageVo(){  
        Page<User> page = userMapper.selectPageVo(new Page<User>(1,2), 20);  
        List<User> users = page.getRecords();  
        users.forEach(System.out::println);  
    }
    

## 3.乐观锁

> **作用：当要更新一条记录的时候，希望这条记录没有被别人更新**

乐观锁的实现方式：

-   取出记录时，获取当前 version
    
-   更新时，带上这个 version
    
-   执行更新时， set version = newVersion where version = oldVersion
    
-   如果 version 不对，就更新失败
    

### 3.1  场景

----------

-   一件商品，成本价是80元，售价是100元。老板先是通知小李，说你去把商品价格增加50元。小李正在玩游戏，耽搁了一个小时。正好一个小时后，老板觉得商品价格增加到150元，价格太高，可能会影响销量。又通知小王，你把商品价格降低30元。
    
-   此时，小李和小王同时操作商品后台系统。小李操作的时候，系统先取出商品价格100元；小王也在操作，取出的商品价格也是100元。小李将价格加了50元，并将100+50=150元存入了数据库；小王将商品减了30元，并将100-30=70元存入了数据库。是的，如果没有锁，小李的操作就完全被小王的覆盖了。
    
-   现在商品价格是70元，比成本价低10元。几分钟后，这个商品很快出售了1千多件商品，老板亏1万多。
    

### 3.2  乐观锁与悲观锁

----------

-   上面的故事，如果是乐观锁，小王保存价格前，会检查下价格是否被人修改过了。如果被修改过了，则重新取出的被修改后的价格，150元，这样他会将120元存入数据库。
    
-   如果是悲观锁，小李取出数据后，小王只能等小李操作完之后，才能对价格进行操作，也会保证最终的价格是120元。
    

### 3.3  模拟修改冲突

----------

-   **数据库中增加商品表**
    
    CREATE TABLE t_product (   
        id BIGINT(20) NOT NULL COMMENT '主键ID',   
        NAME VARCHAR(30) NULL DEFAULT NULL COMMENT '商品名称',   
        price INT(11) DEFAULT 0 COMMENT '价格',   
        VERSION INT(11) DEFAULT 0 COMMENT '乐观锁版本号',   
        PRIMARY KEY (id)   
    );
    
-   **添加一条数据**
    
    INSERT INTO t_product (id, NAME, price) VALUES (1, '外星人笔记本', 100);
    
-   **添加一个实体类`Product`**
    
    @Data  
    public class Product {  
        private Long id;  
        private String name;  
        private Integer price;  
        private Integer version;  
    }
    
-   **添加一个Mapper接口`ProductMapper`**
    
    public interface ProductMapper extends BaseMapper<Product> {}
    
-   **测试方法**
    
    @Test  
    public void testProduct01(){  
        //1.小李获取商品价格  
        Product productLi = productMapper.selectById(1);  
        System.out.println("小李获取的商品价格为：" + productLi.getPrice());  
      
        //2.小王获取商品价格  
        Product productWang = productMapper.selectById(1);  
        System.out.println("小李获取的商品价格为：" + productWang.getPrice());  
      
        //3.小李修改商品价格+50  
        productLi.setPrice(productLi.getPrice()+50);  
        productMapper.updateById(productLi);  
      
        //4.小王修改商品价格-30  
        productWang.setPrice(productWang.getPrice()-30);  
        productMapper.updateById(productWang);  
      
        //5.老板查询商品价格  
        Product productBoss = productMapper.selectById(1);  
        System.out.println("老板获取的商品价格为：" + productBoss.getPrice());  
    }
    
-   **执行结果**
    
    ![image-20220521225803162](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220521225803162.png)
    

### 3.4  乐观锁解决问题

----------

-   **实体类`version`字段添加注解`@Version`**
    
    @Data  
    public class Product {  
        private Long id;  
        private String name;  
        private Integer price;  
        @Version  
        private Integer version;  
    }
    
-   **添加乐观锁插件配置**
    
    @Bean  
    public MybatisPlusInterceptor mybatisPlusInterceptor(){  
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();  
        //添加分页插件  
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));  
        //添加乐观锁插件  
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());  
        return interceptor;  
    }
    
-   **再次执行测试方法**
    
    > 小李查询商品信息：
    > 
    > SELECT id,name,price,version FROM t_product WHERE id=?
    > 
    > 小王查询商品信息：
    > 
    > SELECT id,name,price,version FROM t_product WHERE id=?
    > 
    > 小李修改商品价格，自动将version+1
    > 
    > UPDATE t_product SET name=?, price=?, version=? WHERE id=? AND version=?
    > 
    > Parameters: 外星人笔记本(String), 150(Integer), 1(Integer), 1(Long), 0(Integer)
    > 
    > 小王修改商品价格，此时version已更新，条件不成立，修改失败
    > 
    > UPDATE t_product SET name=?, price=?, version=? WHERE id=? AND version=?
    > 
    > Parameters: 外星人笔记本(String), 70(Integer), 1(Integer), 1(Long), 0(Integer)
    > 
    > 最终，小王修改失败，查询价格：150
    > 
    > SELECT id,name,price,version FROM t_product WHERE id=?
    
-   **优化执行流程**
    
    @Test  
    public void testProduct01(){  
        //1.小李获取商品价格  
        Product productLi = productMapper.selectById(1);  
        System.out.println("小李获取的商品价格为：" + productLi.getPrice());  
      
        //2.小王获取商品价格  
        Product productWang = productMapper.selectById(1);  
        System.out.println("小李获取的商品价格为：" + productWang.getPrice());  
      
        //3.小李修改商品价格+50  
        productLi.setPrice(productLi.getPrice()+50);  
        productMapper.updateById(productLi);  
      
        //4.小王修改商品价格-30  
        productWang.setPrice(productWang.getPrice()-30);  
        int result = productMapper.updateById(productWang);  
        if(result == 0){  
            //操作失败，重试  
            Product productNew = productMapper.selectById(1);  
            productNew.setPrice(productNew.getPrice()-30);  
            productMapper.updateById(productNew);  
        }  
      
        //5.老板查询商品价格  
        Product productBoss = productMapper.selectById(1);  
        System.out.println("老板获取的商品价格为：" + productBoss.getPrice());  
    }
    
    ![image-20220521230448577](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220521230448577.png)
    

# 七、通用枚举

> 表中的有些字段值是固定的，例如性别（男或女），此时我们可以使用MyBatis-Plus的通用枚举来实现

-   **数据库表添加字段`sex`**
    
    ![image-20220521231317777](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220521231317777.png)
    
-   **创建通用枚举类型**
    
    @Getter  
    public enum SexEnum {  
        MALE(1, "男"),  
        FEMALE(2, "女");  
      
        @EnumValue //将注解所标识的属性的值存储到数据库中  
        private int sex;  
        private String sexName;  
      
        SexEnum(Integer sex, String sexName) {  
            this.sex = sex;  
            this.sexName = sexName;  
        }  
    }
    
-   **User实体类中添加属性sex**
    
    public class User {  
        private Long id;  
        @TableField("username")  
        private String name;  
        private Integer age;  
        private String email;  
      
        @TableLogic  
        private int isDeleted;  //逻辑删除  
      
        private SexEnum sex;  
    }
    
-   **配置扫描通用枚举**
    
    #MyBatis-Plus相关配置  
    mybatis-plus:  
      #指定mapper文件所在的地址  
      mapper-locations: classpath:mapper/*.xml  
      configuration:  
        #配置日志  
        log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
      global-config:  
        banner: off  
        db-config:  
          #配置mp的主键策略为自增  
          id-type: auto  
          # 设置实体类所对应的表的统一前缀  
          table-prefix: t_  
      #配置类型别名所对应的包  
      type-aliases-package: com.atguigu.mybatisplus.pojo  
      # 扫描通用枚举的包  
      type-enums-package: com.atguigu.mybatisplus.enums
    
-   **执行测试方法**
    
    @Test  
    public void test(){  
        User user = new User();  
        user.setName("admin");  
        user.setAge(33);  
        user.setSex(SexEnum.MALE);  
        int result = userMapper.insert(user);  
        System.out.println("result:"+result);  
    }
    

# 八、多数据源

> 适用于多种场景：纯粹多库、 读写分离、 一主多从、 混合模式等

场景说明：

我们创建两个库，分别为：`mybatis_plus`（以前的库不动）与`mybatis_plus_1`（新建），将mybatis_plus库的`product`表移动到mybatis_plus_1库，这样每个库一张表，通过一个测试用例分别获取用户数据与商品数据，如果获取到说明多库模拟成功

## 1.创建数据库及表

-   **创建数据库`mybatis_plus_1`和表`product**
    
    CREATE DATABASE `mybatis_plus_1` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;  
    use `mybatis_plus_1`;   
    CREATE TABLE product (   
        id BIGINT(20) NOT NULL COMMENT '主键ID',   
        name VARCHAR(30) NULL DEFAULT NULL COMMENT '商品名称',   
        price INT(11) DEFAULT 0 COMMENT '价格',   
        version INT(11) DEFAULT 0 COMMENT '乐观锁版本号',   
        PRIMARY KEY (id)   
    );
    
-   **添加测试数据**
    
    INSERT INTO product (id, NAME, price) VALUES (1, '外星人笔记本', 100);
    
-   **删除`mybatis_plus`库中的`product`表**
    
    use mybatis_plus;   
    DROP TABLE IF EXISTS product;
    

## 2.新建工程引入依赖

> **自行新建一个Spring Boot工程并选择MySQL驱动及Lombok依赖**

**引入MyBaits-Plus的依赖及多数据源的依赖**

<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-boot-starter</artifactId>  
    <version>3.5.1</version>  
</dependency>  
  
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>dynamic-datasource-spring-boot-starter</artifactId>  
    <version>3.5.0</version>  
</dependency>

## 3.编写配置文件

spring:  
  # 配置数据源信息  
  datasource:  
    dynamic:  
      # 设置默认的数据源或者数据源组,默认值即为master  
      primary: master  
      # 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源  
      strict: false  
      datasource:  
        master:  
          url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false  
          driver-class-name: com.mysql.cj.jdbc.Driver  
          username: root  
          password: 132537  
        slave_1:  
          url: jdbc:mysql://localhost:3306/mybatis_plus_1?characterEncoding=utf-8&useSSL=false  
          driver-class-name: com.mysql.cj.jdbc.Driver  
          username: root  
          password: 132537

## 4.创建实体类

-   新建一个`User`实体类（如果数据库表名有t_前缀记得配置）
    
    @Data  
    public class User {  
        private Long id;  
        private String name;  
        private Integer age;  
        private String email;  
    }
    
-   新建一个实体类`Product`
    
    @Data  
    public class Product {  
        private Long id;  
        private String name;  
        private Integer price;  
        private Integer version;  
    }
    

## 5.创建Mapper及Service

-   新建接口`UserMapper`
    
    public interface UserMapper extends BaseMapper<User> {}
    
-   新建接口`ProductMapper`
    
    public interface ProductMapper extends BaseMapper<Product> {}
    
-   新建Service接口`UserService`指定操作的数据源
    
    @DS("master") //指定操作的数据源，master为user表  
    public interface UserService extends IService<User> {}
    
-   新建Service接口`ProductService`指定操作的数据源
    
    @DS("slave_1")  
    public interface ProductService extends IService<Product> {}
    
-   自行建立Service的实现类
    
    ...
    

## 6.编写测试方法

> **记得在启动类中添加注解`@MapperScan()`**

class TestDatasourceApplicationTests {  
 @Resource  
 UserService userService;  
​  
 @Resource  
 ProductService productService;  
​  
 @Test  
 void contextLoads() {  
 User user = userService.getById(1L);  
 Product product = productService.getById(1L);  
 System.out.println("User = " + user);  
 System.out.println("Product = " + product);  
 }  
​  
}

![image-20220522113049945](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522113049945.png)

# 九、MyBatisX插件

> MyBatis-Plus为我们提供了强大的mapper和service模板，能够大大的提高开发效率。
> 
> 但是在真正开发过程中，MyBatis-Plus并不能为我们解决所有问题，例如一些复杂的SQL，多表联查，我们就需要自己去编写代码和SQL语句，我们该如何快速的解决这个问题呢，这个时候可以使用MyBatisX插件。
> 
> MyBatisX一款基于 IDEA 的快速开发插件，为效率而生。

## 1.安装MyBatisX插件

> **打开IDEA，File-> Setteings->Plugins->MyBatisX，搜索栏搜索MyBatisX然后安装。**

![image-20220522115718361](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522115718361.png)

## 2.快速生成代码

-   新建一个Spring Boot项目引入依赖（创建工程时记得勾选lombok及mysql驱动）
    
    <dependency>  
     <groupId>com.baomidou</groupId>  
     <artifactId>mybatis-plus-boot-starter</artifactId>  
     <version>3.5.1</version>  
    </dependency>  
    ​  
    <dependency>  
     <groupId>com.baomidou</groupId>  
     <artifactId>dynamic-datasource-spring-boot-starter</artifactId>  
     <version>3.5.0</version>  
    </dependency>
    
-   配置数据源信息
    
    spring:  
     datasource:  
     type: com.zaxxer.hikari.HikariDataSource  
     driver-class-name: com.mysql.cj.jdbc.Driver  
     url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false  
     username: root  
     password: 132537
    
-   在IDEA中与数据库建立链接
    
    ![image-20220522120758740](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522120758740.png)
    
-   填写数据库信息并保存
    
    ![image-20220522121434468](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522121434468.png)
    
-   找到我们需要生成的表点击右键
    
    ![image-20220522121613909](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522121613909.png)
    
-   填写完信息以后下一步
    
    ![image-20220522122127649](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522122127649.png)
    
-   继续填写信息
    
    ![image-20220522122525598](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522122525598.png)
    
-   **大功告成（真特么好用yyds）**
    
    ![image-20220522122612334](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522122612334.png)
    

## 3.快速生成CRUD

> MyBaitsX可以根据我们在Mapper接口中输入的方法名快速帮我们生成对应的sql语句

![image-20220522123143852](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522123143852.png)

![image-20220522123202310](https://image-bed-vz.oss-cn-hangzhou.aliyuncs.com/MyBatis-Plus/image-20220522123202310.png)

# 十、致谢

感谢尚硅谷杨博超老师：[https://www.bilibili.com/video/BV12R4y157Be?p=1](https://www.bilibili.com/video/BV12R4y157Be?p=1)

感谢MyBatis-Plus作者苞米豆：[https://baomidou.com/](https://baomidou.com/)

感谢自己又坚持学习了一门课程：[https://www.oz6.cn/](https://www.oz6.cn/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUwNDY0NjEzMSwxOTI2NTY1Nzc4LC0yMD
cyNjc2NTQ4LDE5NDUyMDg4MzQsMTMzNTYyNTUzOSwtMjYzODk3
OTgxLDU1ODg3NzI3OCwtMTExNjQ5ODI0MSwxMjkxNjk4MTgsMj
AzMDQ1MjA1NywtNzQ1MDA3NzMzXX0=
-->