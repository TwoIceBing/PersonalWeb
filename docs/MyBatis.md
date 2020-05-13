# MyBatis

# 一、基础

## 1.1 MyBatis概念

1. mybatis是一个优秀的基于Java的持久层框架，它内部封装了JDBC，使开发者只需要关注sql语句本身，而不需要花费精力去处理加载驱动，创建连接，创建statement等繁杂的过程。
2.  mybatis通过xml或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。
3. mybatis使用ORM（Object Relational Mapping）实现了结果集的封装

## 1.2 Mybatis和Hibernate对比

1. sql 优化方面
   - Hibernate 不需要编写大量的 SQL，就可以完全映射，提供了日志、缓存、级联（级联比 MyBatis 强大）等特性，此外还提供 HQL（Hibernate Query Language）对 POJO 进行操作。但会多消耗性能。
   - MyBatis 手动编写 SQL，支持动态 SQL、处理列表、动态生成表名、支持存储过程。工作量相对大些。
2. 开发方面
   - MyBatis 是一个半自动映射的框架，因为 MyBatis 需要手动匹配 POJO、SQL 和映射关系。
   - Hibernate 是一个全表映射的框架，只需提供 POJO 和映射关系即可。
3. Hibernate的优势
   - Hibernate 的 DAO 层开发比 MyBatis 简单，Mybatis需要维护 SQL 和结果映射。
   - Hibernate 对对象的维护和缓存要比 MyBatis 好，对增删改查的对象的维护要方便。
   - Hibernate 数据库移植性很好，MyBatis 的数据库移植性不好，不同的数据库需要写不同 SQL。
   - Hibernate 有更好的二级缓存机制，可以使用第三方缓存。MyBatis 本身提供的缓存机制不佳。
4. MyBatis优势
   - MyBatis 可以进行更为细致的 SQL 优化，可以减少查询字段。
   - MyBatis 容易掌握，而 Hibernate 门槛较高。
5. 总结：MyBatis 是一个小巧、方便、高效、简单、直接、半自动化的持久层框架，Hibernate 是一个强大、方便、高效、复杂、间接、全自动化的持久层框架。对于性能要求不太苛刻的系统，比如管理系统、ERP 等推荐使用 Hibernate，而对于性能要求高、响应快、灵活的系统则推荐使用 MyBatis。

## 1.3 mybatis工作原理

1. mybatis工作流程图

![5-1ZF4130T31N](E:\Java\星期天\image\5-1ZF4130T31N.png)

2. 每个流程的说明：

   （1）：读取 MyBatis 配置文件：mybatis-config.xml为MyBatis 的全局配置文件，配置了 MyBatis 的运行环境等信息，例如数据库连接信息。

   （2）：加载映射文件，映射文件即 SQL 映射文件，该文件中配置了操作数据库的 SQL 语句，需要在 MyBatis 配置文件 mybatis-config.xml 中加载。mybatis-config.xml 文件可以加载多个映射文件，每个文件对应数据库中的一张表。

   （3）：构造会话工厂：通过 MyBatis 的环境等配置信息构建会话工厂 SqlSessionFactory。

   （4）：创建会话对象：由会话工厂创建 SqlSession 对象，该对象中包含了执行 SQL 语句的所有方法。

   （5）：Executor 执行器：MyBatis 底层定义了一个 Executor 接口来操作数据库，它将根据 SqlSession 传递的参数动态地生成需要执行的 SQL 语句，同时负责查询缓存的维护。

   （6）：MappedStatement 对象：在 Executor 接口的执行方法中有一个 MappedStatement 类型的参数，该参数是对映射信息的封装，用于存储要映射的 SQL 语句的 id、参数等信息。

   （7）：输入参数映射：输入参数类型可以是 Map、List 等集合类型，也可以是基本数据类型和 POJO 类型。输入参数映射过程类似于 JDBC 对 preparedStatement 对象设置参数的过程。

   （8）：输出结果映射：输出结果类型可以是 Map、 List 等集合类型，也可以是基本数据类型和 POJO 类型。输出结果映射过程类似于 JDBC 对结果集的解析过程。

## 1.4 MyBatis依赖

1. MyBatis依赖

   ```xml
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.4.6</version>
   </dependency>
   ```

2.  MyBatis-Spring整合依赖

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.2</version>
    </dependency>
    ```

3. mysql依赖

   ```xml
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.5</version>
   </dependency>
   ```



# 二、MyBatis核心组件

## 2.1 概述

1. MyBatis有4个核心组件：
   - SqlSessionFactoryBuilder（构造器）：会根据配置或者代码来生成 SqlSessionFactory，采用的是分步构建的 Builder 模式。
   - SqlSessionFactory（工厂接口）：依靠它来生成 SqlSession，使用的是工厂模式。
   - SqlSession（会话）：一个既可以发送 SQL 执行返回结果，也可以获取 Mapper 的接口。在现有的技术中，一般我们会让其在业务逻辑代码中“消失”，而使用的是 MyBatis 提供的 SQL Mapper 接口编程技术，它能提高代码的可读性和可维护性。
   - SQL Mapper（映射器）:MyBatis 新设计存在的组件，它由一个 Java 接口和 XML 文件（或注解）构成，需要给出对应的 SQL 和映射规则。它负责发送 SQL 去执行，并返回结果。
2. MyBatis有两种配置文件
   - MyBatis的基础配置文件：通常只有一个，主要是配置一些最基本的上下文参数和运行环境，可以命名为：mybatis-config.xml或SqlMapConfig.xml
   - MyBatis的SQL映射文件：用来配置配置映射关系、SQL、参数等信息；通过每个Dao（每个表）都有一份，命名一般以Dao的文件名为前缀

## 2.2 构建SqlSessionFactory

1. 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为中心的，SqlSessionFactory 是一个接口，在 MyBatis 中它存在两个实现类

   - SqlSessionManager ：多线程的环境中用这个，其实也是依靠的DefaultSqlSessionFactory，他实现了SqlSessionFactory和SqlSession
   - DefaultSqlSessionFactory：一般用这个

2. MyBatis采用构造器SqlSessionFactoryBuilder来构造会话工厂SqlSessionFactory，SqlSessionFactory 唯一的作用就是生产 MyBatis 的核心接口对象 SqlSession，所以它的责任是唯一的，所以一般采用单例模式处理它

3. 构建一个SqlSessionFactory可以通过mybatis配置文件构建或者Java代码构建则两种方式，Java代码的构建一般不用（除非在配置文件中，存在加密过的数据库用户名和密码，需要我们在生成 SqlSessionFactory 前解密为明文的时候才使用Java代码去构建）

4. mybatis-config.xml内容如下（需要有mysql依赖）

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <!--别名-->
       <typeAliases>
           <typeAliases alias="user" type="com.jzp.domain.User"/>
       </typeAliases>
   	<!-- 数据库环境-->
       <environments default="development">
   		<!-- 配置mysql环境 -->
           <environment id="development">
   			<!-- 配置事务类型 -->
               <transactionManager type="JDBC"/>
   			<!-- 配置数据源（连接池）-->
               <dataSource type="POOLED">
   				<!-- 配置连接数据库的4个基本信息-->
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                   <property name="username" value="root"/>
                   <property name="password" value="1415"/>
               </dataSource>
           </environment>
       </environments>
   	<!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件 -->
       <mappers>
           <mapper resource="com/jzp/dao/IUserDao.xml"/>
       </mappers>
   </configuration>
   ```

   - 一般来说environments中的default参数和environment中的id参数一致，如果修改两者的值，可以在运行环境的SqlSessionFactoryBuilder.build(Stream,environment)中填入environment参数。
   - 配置事务类型值有：
     - JDBC：利用JDBC方式处理事务(commit rollback close)
     - MANAGED：将事务交由其他组件去托管(spring, jboss)，默认会关闭连接
   - 配置数据源（连接池）种类有：
     - UNPOOLED：传统JDBC模式，每次访问数据库，都需要打开，关闭等操作
     - POOLED：使用数据库连接池，一般都是用这个
     - JNDI：tomcat内置数据库连接池，tomcat自带
   - 别名：定义别名后，在 MyBatis 上下文中就可以使用别名去代替全限定名了。

5. 生成 SqlSessionFactory

   ```java
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   
   psvm{
       InputStream inputStream = null;
       try {
           inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
           SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
           SqlSessionFactory factory = builder.build(inputStream);
           //接下来就是工厂创建SqlSession，获取Dao等等
       }catch (IOException e) {
       	e.printStackTrace();
   	}finally{
           inputStream.close();
       }
   }
   ```

## 2.3 生产SqlSession

1. SqlSession是MyBatis的核心接口，有两个实现类：

   - DefaultSqlSession：单线程使用的
   - SqlSessionManager：多线程环境下使用

2. SqlSession的作用有：

   - 获取 Mapper 接口。
   - 发送 SQL 给数据库。
   - 控制数据库事务。

3. 使用

   ```java
   try {
       //使用工厂创建SqlSession，factory即SqlSessionFactory的实例化对象
       SqlSession session = factory.openSession();
       // 提交事务
   	session.commit();
   }catch (IOException e) {
       session.rollback();  // 回滚事务
   }finally{
       // 在 finally 语句中确保资源被顺利关闭
       if(session != null){
           session.close();
       }
   }
   ```

## 2.4 实现mapper

1. 映射器（mapper）是 MyBatis 中最重要、最复杂的组件，可以使用xml或注解实现，可以配置以下内容：

   - 描述映射规则。

   - 提供 SQL 语句，并可以配置 SQL 参数类型、返回类型、缓存刷新等信息。
   - 配置缓存。
   - 提供动态 SQL。

2. 提供Dao的是接口而不是类，MyBatis运用了动态代理技术使得接口能运行起来

3. MyBatis的映射器有两种实现方式，即：

   - XML 文件形式：官方建议使用这种方式，可以动态SQL和XML之间的相互引入
   - 注解形式： 如果SQL 比较复杂，会使Java代码的可读性变差；复杂的SQL逻辑注解是无法实现的

4. XML 文件形式需要想在mybatis的配置文件中声明mapper路径，如下：

   ```xml
   <mappers>
       <mapper resource="com/jzp/dao/IUserDao.xml"/>
   </mappers>
   ```

   然后在resource对应路径下创建文件，并为Dao中的函数进行映射

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.jzp.dao.IUserDao">
       <!--    配置查询所有-->
       <select id="findAll" resultType="com.jzp.domain.User">select * from user</select>
   </mapper>
   ```

   - namespace ：对应的是一个接口的全限定名，于是 MyBatis 上下文就可以通过它找到对应的接口。
   - MyBatis会自动的讲返回值和POJO 的属性映射起来，但POJO的属性名需要和表中字段名一致；如果不一致，可以在SQL语句中加上别名

5. 注解的形式可以直接在Dao方法上加注解，并修改mybatis的配置文件mapper中的resource路径

   ```xml
   <mappers>
       <mapper resource="com/jzp/dao/IUserDao"/>
   </mappers>
   ```
   
   注解：

   ```java
package com.jzp.dao;
   import org.apache.ibatis.annotations.Select;
   import com.jzp.domain.User;
   
   public interface IUserDao {
       @Select("select * from user")
       List<User> findAll();
   }
   ```

## 2.5 执行SQL

1. MyBatis执行SQL主要有两种方式，即：

   - 通过 SqlSession 发送 SQL
   - 用 Mapper 接口发送 SQL，建议采用这个；有点：
     - 消除 SqlSession 带来的功能性代码，提高可读性
     - 使用 Mapper 接口则是完全面向对象的语言，更能体现业务的逻辑。
     - 使用 Mapper 接口如果函数不存在会直接报错，而SqlSession只有在运行时才知道是否会参数错误

2. 通过 SqlSession 发送 SQL

   ```java
   List<User> list = (List<User>)sqlSession.select("com.jzp.dao.IUserDao.findAll");
   //如果id唯一，可以直接使用id
   List<User> list = (List<User>)sqlSession.select("findAll");
   //如果只查询一条语句，可以使用selectOne
   User user = (User)sqlSession.selectOne("findById",1);
   ```

3. 用 Mapper 接口发送 SQL

   ```java
   UserMapper userMapper = sqlSession.getMapper(IUserDao.class);
   List<User> list = userMapper.findAll();
   ```

## 2.6 组件的生命周期

1. SqlSessionFactoryBuilder：作用在于创建 SqlSessionFactory，创建成功后，SqlSessionFactoryBuilder 就失去了作用；因此SqlSessionFactoryBuilder最好存在于创建 SqlSessionFactory 的方法中，而不要让其长期存在
2. SqlSessionFactory ：可以被认为是一个数据库连接池，作用是创建 SqlSession 接口对象，生命周期存在于整个 MyBatis 的应用之中，所以一旦创建就要长期保存它；SqlSessionFactory最好只有一个，如果创建多个就相当于存在多个数据库连接池，这是错误的选择，所以一般的应用中SqlSessionFactory 作为一个单例存在
3. SqlSession：相当于一个数据库连接（Connection 对象），应该存活在一个业务请求中，处理完整个请求后，应该关闭这条连接，让它归还给 SqlSessionFactory
4. Mapper ：Mapper 代表的是一个请求中的业务处理，它的生命周期应该小于等于 SqlSession 的生命周期；在一个请求中，一旦处理完了相关的业务，就应该废弃它

# 三、mybatis配置

## 3.1 Mybatis配置文件

1. Mybatis的配置文件有如下属性：

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration><!-- 配置 -->
       <properties /><!-- 属性 -->
       <settings /><!-- 设置 -->
       <typeAliases /><!-- 类型命名 -->
       <typeHandlers /><!-- 类型处理器 -->
       <objectFactory /><!-- 对象工厂 -->
       <plugins /><!-- 插件 -->
       <environments><!-- 配置环境 -->
           <environment><!-- 环境变量 -->
               <transactionManager /><!-- 事务管理器 -->
               <dataSource /><!-- 数据源 -->
           </environment>
       </environments>
       <databaseIdProvider /><!-- 数据库厂商标识 -->
       <mappers /><!-- 映射器 -->
   </configuration>
   ```

2. **注意！，MyBatis 配置项的顺序不能颠倒。**如果颠倒了它们的顺序，那么在 MyBatis 启动阶段就会发生异常，导致程序无法运行。

## 3.2 properties元素

1. properties 属性可以给系统配置一些运行参数，可以放在 XML 文件或者 properties 文件中

2. 一般properties的一些参数都存放在一个properties文件中，然后通过properties标签的resource属性来引入

   ```xml
   <properties resource="db.properties"/>
   ```

3. 接下来就可以使用${}使用里面的参数

## 3.3 settings元素

1. settings 是最复杂的配置，它能深刻影响 MyBatis 底层的运行，但是在大部分情况下使用默认值便可以运行，所以在大部分情况下不需要大量配置它

2. settings需要用到的有

   - 自动映射
   - 驼峰命名映射
   - 级联规则
   - 是否启动缓存
   - 执行器（Executor）类型
   - 等等

3. 一个全量的配置样例，如下所示

   ```xml
   <settings>
       <setting name="cacheEnabled" value="true"/>
       <setting name="lazyLoadingEnabled" value="true"/>
       <setting name="multipleResultSetsEnabled" value="true"/>
       <setting name="useColumnLabel" value="true"/>
       <setting name="useGeneratedKeys" value="false"/>
       <setting name="autoMappingBehavior" value="PARTIAL"/>
       <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
       <setting name="defaultExecutorType" value="SIMPLE"/>
       <setting name="defaultStatementTimeout" value="25"/>
       <setting name="defaultFetchSize" value="100"/>
       <setting name="safeRowBoundsEnabled" value="false"/>
       <setting name="mapUnderscoreToCamelCase" value="false"/>
       <setting name="localCacheScope" value="SESSION"/>
       <setting name="jdbcTypeForNull" value="OTHER"/>
       <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
   </settings>
   ```

4. 几个常用的解释：

   - useGeneratedKeys：配置全局主键自增
   
   - cacheEnabled：该配置影响所有映射器中配置缓存的全局开关；取值true|false，默认true，
   - lazyLoadingEnabled：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。在特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态 ；取值true|false，默认false
   - aggressiveLazyLoading：当启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载；反之，每种属性将会按需加载；取值true|felse，默认3.4.1之前true，之后false
   - autoMappingBehavior：指定 MyBatis 应如何自动映射列到字段或属性。取值NONE|PARTIAL|FULL，默认PARTIAL
   - mapUnderscoreToCamelCase：是否开启自动驼峰命名规则映射，即将数据库的字段以驼峰命名映射，取值true|false，默认false，即等值映射
   - defaultExecutorType：配置默认的执行器。取值：SIMPLE|REUSE|BATCH，默认SIMPLE；SIMPLE 是普通的执行器；REUSE 会重用预处理语句（prepared statements）；BATCH 执行器将重用语句并执行批量更新 

## 3.4 typeAliases元素

1. typeAliases（别名）由于类的全限定名称很长，需要大量使用的时候，总写那么长的名称不方便。在 MyBatis 中允许定义一个简写来代表这个类，这就是别名

2. 别名分为系统定义别名和自定义别名。由类 TypeAliasRegistry（org.apache.ibatis.type.TypeAliasRegistry）去定义。别名不区分大小写

3. 自定义别名

   ```xml
   <typeAliases><!--别名-->
   	<typeAliases alias="user" type="com.jzp.domain.User"/>
   </typeAliases>
   ```

4. 如果类太多，这样会导致别名过多，可以让他自动生成别名；MyBatis 支持扫描别名，指定相应的包就可以自动扫描了，MyBatis会自动将类名的第一个字母小写

   ```xml
   <typeAliases><!--别名-->
       <package name="com.jzp.domain"/>
   </typeAliases>
   ```

5. 也可以在类上面加注解

   ```java
   package com.jzp.domain;
   @Alias("user")
   public Class User {
       ......
   }
   ```

## 3.5 typeHandlers元素

1. typeHandlers（类型处理器|类型转换器），负责Java数据类型（javaType）和数据库数据类型（jdbcType）之间的互相转换，一般来说，不需要去自定义typeHandlers，MyBatis 会探测应该使用什么类型的 typeHandler 进行处理；

2. 对于一些特殊数据类型的场景，才需要使用自定义的 typeHandler 去处理类型之间的转换

3. 系统定义的typeHandler

   | 类型处理器                 | Java类型                   | JDBC类型                                                     |
   | -------------------------- | -------------------------- | ------------------------------------------------------------ |
   | BooleanTypeHandler         | java.lang.Boolean，boolean | 数据库兼容的 BOOLEAN                                         |
   | ByteTypeHandler            | java.lang.Byte，byte       | 数据库兼容的 NUMERIC 或 BYTE                                 |
   | ShortTypeHandler           | java.lang.Short，short     | 数据库兼容的 NUMERIC 或 SHORT INTEGER                        |
   | IntegerTypeHandler         | java.lang.Integer，int     | 数据库兼容的 NUMERIC 或 INTEGER                              |
   | LongTypeHandler            | java.lang.Long，long       | 数据库兼容的 NUMERIC 或 LONG INTEGER                         |
   | FloatTypeHandler           | java.lang.Float，float     | 数据库兼容的 NUMERIC 或 FLOAT                                |
   | DoubleTypeHandler          | java.lang.Double，double   | 数据库兼容的 NUMERIC 或 DOUBLE                               |
   | BigDecimalTypeHandler      | java.math.BigDecimal       | 数据库兼容的 NUMERIC 或 DECIMAL                              |
   | StringTypeHandler          | java.lang.String           | CHAR、VARCHAR                                                |
   | ClobReaderTypeHandler      | java.io.Reader             | ——                                                           |
   | ClobTypeHandler            | java.lang.String           | CLOB、LONGVARCHAR                                            |
   | NStringTypeHandler         | java.lang.String           | NVARCHAR、NCHAR                                              |
   | NClobTypcHandler           | java.lang.String           | NCLOB                                                        |
   | BlobInputStreamTypeHandler | java.io.InputStream        | ——                                                           |
   | ByteArrayTypeHandler       | byte[]                     | 数据库兼容的字节流类型                                       |
   | BlobTypeHandler            | byte[]                     | BLOB、LONGVARBINARY                                          |
   | DateTypeHandler            | java.util.Date             | TIMESTAMP                                                    |
   | DateOnlyTypeHandler        | java.util.Date             | DATE                                                         |
   | TimeOnlyTypeHandler        | java.util.Date             | TIME                                                         |
   | SqlTimestampTypeHandler    | java.sql.Timestamp         | TIMESTAMP                                                    |
   | SqlDateTypeHandler         | java.sql.Date              | DATE                                                         |
   | SqlTimeTypeHandler         | java.sql.Time              | TIME                                                         |
   | ObjectTypeHandler          | Any                        | OTHER或未指定类型                                            |
   | EnumTypeHandler            | Enumeration Type           | VARCHAR 任何兼容的字符串类型，存储枚举的名称（而不是索引）   |
   | EnumOrdinalTypeHandler     | Enumeration Type           | 任何兼容的 NUMERIC 或 DOUBLE 类型，存储枚举的索引（而不是名称） |

4. 自定义类型转换器，需要实现TypeHandler接口或继承BaseTypeHandler类（建议），如将Java boolean类型转换为jdbc int类型：

   ```java
   public class BooleanAndIntConvert extends BaseTypeHandler<boolean> {
       //将Java boolean类型转换为jdbc int类型
       @Override
       public void setNonNullParameter(PreparedStatement ps, int i, boolean b, JdbcType jdbcType) throws SQLException {
           if (b) {
               ps.setInt(i, 0);
           } else {
               ps.setInt(i, 1);
           }
       }
       
       //将jdbc int类型转换为Java boolean类型
       @Override
       public boolean getNullableResult(ResultSet rs, String s) throws SQLException {
           int sexNum = rs.getInt(s);
           return sexNum == 1;
       }
       ...
   }
   ```
   
   ```xml
   <typeHandlers>
       <typeHandler jdbcType="int" javaType="boolean" handler="com.mybatis.test.BooleanAndIntConvert"/>
   </typeHandlers>
   ```
   
   

## 3.6 ObjectFactory元素

1. ObjectFactory（对象工厂）：当创建结果集时，MyBatis 会使用一个对象工厂来完成创建这个结果集实例，默认使用DefaultObjectFactory（org.apache.ibatis.reflection.factory.DefaultObjectFactory）

2. MyBatis 允许注册自定义的 ObjectFactory。如果自定义，则需要实现接口org.apache.ibatis.reflection.factory.ObjectFactory，并给予配置；在更多的情况下，都会考虑继承系统已经实现好的 DefaultObjectFactory，通过一定的改写来完成我们所需要的工作

3. 配置自定义的ObjectFactory

   ```xml
   <objectFactory type="com.jzp.MyObjectFactory">
       <property name="prop1" value="value1" />
   </objectFactory>
   ```

## 3.7 environments元素

1. environments（运行环境），主要的作用是配置数据库信息，它可以配置多个数据库，一般而言只需要配置其中的一个就可以了

2. 主要有两个可以配置的元素：

   - 事务管理器（transactionManager）
   - 数据源（dataSource）

3. transactionManager的主要的工作就是提交（commit）、回滚（rollback）和关闭（close）数据库的事务，他的类型有两个，即：

   - JDBC：使用 JdbcTransactionFactory 生成的 JdbcTransaction 对象实现，以 JDBC 的方式对数据库的提交和回滚进行操作
   - MANAGED： 使用 ManagedTransactionFactory 生成的 ManagedTransaction 对象实现，它的提交和回滚方法不用任何操作，而是把事务交给容器（Spring、jboss等）处理，默认情况下，它会关闭连接，如果不想关闭可以将 closeConnection 属性设置为 false
   - 自定义事务管理器：需要自定义一个事务工厂（实现TransactionFactory接口），自定义事务实现类（实现 Transaction 接口，并继承JdbcTransaction）

   ```xml
   <transactionManager type="JDBC"/>
   <transactionManager type="MANAGED"/>
   ```

4. dataSource：负责配置数据库，数据库通过 PooledDataSource Factory、UnpooledDataSourceFactory 和 JndiDataSourceFactory 三个工厂类来提供，这三个工厂类都能产生一个实现了 DataSource 接口的数据库连接对象。三个工厂类就有三个数据源，即：

   - UNPOOLED：非数据库池，每次请求都会打开一个新的数据库连接，所以创建会比较慢。在一些对性能没有很高要求的场合可以使用它。有以下属性：

     - driver：数据库驱动名
     - url：连接数据库的 URL
     - username：用户名
     - password：密码
     - defaultTransactionIsolationLevel：默认的连接事务隔离级别

   - POOLED：数据库连接池，出了拥有UNPOOLED的所有属性外，还有：

     - poolMaximumActiveConnections：是在任意时间都存在的活动（也就是正在使用）连接数量，默认值为 10
     - poolMaximumIdleConnections：是任意时间可能存在的空闲连接数
     - poolMaximumCheckoutTime：在被强制返回之前，池中连接被检出（checked out）的时间，默认值为 20 000 毫秒（即 20 秒）
     - poolTimeToWait：是一个底层设置，如果获取连接花费相当长的时间，它会给连接池打印状态日志，并重新尝试获取一个连接（避免在误配置的情况下一直失败），默认值为 20 000 毫秒（即 20 秒）
     - poolPingQuery：为发送到数据库的侦测查询，用来检验连接是否处在正常工作秩序中，并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动失败时带有一个恰当的错误消息
     - poolPingEnabled：为是否启用侦测查询。若开启，也必须使用一个可执行的 SQL 语句设置 poolPingQuery 属性（最好是一个非常快的 SQL），默认值为 false。
     - poolPingConnectionsNotUsedFor：为配置 poolPingQuery 的使用频度。这可以被设置成匹配具体的数据库连接超时时间，来避免不必要的侦测，默认值为 0（即所有连接每一时刻都被侦测——仅当 poolPingEnabled 为 true 时适用）。

   - JNDI：数据源 JNDI 的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。这种数据源配置只需要两个属性：

     - initial_context：用来在 InitialContext 中寻找上下文（即，initialContext.lookup（initial_context））。initial_context 是个可选属性，如果忽略，那么 data_source 属性将会直接从 InitialContext 中寻找。

     - data_source：是引用数据源实例位置上下文的路径。当提供 initial_context 配置时，data_source 会在其返回的上下文中进行查找；当没有提供 initial_context 时，data_source 直接在 InitialContext 中查找。

   - 自定义数据源：实现DataSourceFactory接口，然后在dataSource中引用就是

   ```xml
   <dataSource type="UNPOOLED">
   <dataSource type="POOLED">
   <dataSource type="JNDI">
   ```

# 四、mybatis数据库语句

## 4.1 概述

1. MyBatis提供以下标签
   - \<insert>：实现添加
   - \<delete>：实现删除
   - \<update>：实现更新
   - \<select>：实现查询
   - \<sql>：定义 SQL 语句的一部分（代码片段），以方便后面的 SQL 语句引用它，例如反复使用的列名。
   - \<include>：引用被\<sql>标签定义的代码片段
   - \<resultMap >：结果映射集，用来定义映射规则、级联的更新以及定义类型转化器
   - \<if>：条件判断，相当于if语句
   - \<choose>：相当于switch
   - \<when>：相当于case
   - \<otherwise>：相当于default
   - \<trim>：在自己包含的内容前加上某些前缀或后缀，对应的属性是 prefixOverrides 和 suffixOverrides
   - \<where>：相当于SQL中的where
   - \<set>：用于动态更新列
   - \<foreach> ：类似于jsp中的foreach标签，用于迭代 SQL中的集合
   - \<bind>：从表达式中创建一个变量，并绑定到上下文中，常用语模糊查询
2. 标签的常用属性
   - id：和 Mapper 的命名空间组合起来使用，是唯一标识符，供 MyBatis 调用
   - parameterType：表示传入 SQL 语句的参数类型的全限定名或别名。它是一个可选属性，MyBatis 能自动推断出具体传入语句的参数
   - resultType：SQL 语句执行后返回的类型（全限定名或者别名）。如果是集合类型，返回的是集合元素的类型，返回时可以使用 resultType 或 resultMap 之一。返回值无论是有一个还是多个，都是对象
   - resultMap：是映射集的引用，与 \<resultMap> 元素一起使用，返回时可以使用 resultType 或 resultMap 之一
   - flushCache：用于设置在调用 SQL 语句后是否要求 MyBatis 清空之前查询的本地缓存和二级缓存，默认值为 false，如果设置为 true，则任何时候只要 SQL 语句被调用都将清空本地缓存和二级缓存
   - useCache：启动二级缓存的开关，默认值为 true，表示将査询结果存入二级缓存中
   - timeout：用于设置超时参数，单位是秒（s），超时将抛出异常
   - fetchSize：获取记录的总条数设定
   - statementType：告诉 MyBatis 使用哪个 JDBC 的 Statement 工作，取值为STATEMENT（Statement）|PREPARED（PreparedStatement）|CALLABLE（CallableStatement）
3. 占位符
   * #{}：自动给String加上''，可以防止sql注入
   * ${}：原样输出，但是适合值在sql语句中作为变量时，如动态排序

## 4.2 insert

1. \<insert> 元素用于映射插入语句，MyBatis 执行完一条插入语句后将返回一个整数表示其影响的行数

2. 它还有以下属性：

   - keyProperty：将插入或更新操作时的返回值赋给 PO 类的某个属性，通常会设置为主键对应的属性。如果是联合主键，可以将多个值用逗号隔开。
   - keyColumn：用于设置第几列是主键，当主键列不是表中的第 1 列时需要设置。如果是联合主键，可以将多个值用逗号隔开。
   - useGeneratedKeys：默认false，当为true时，会使用 JDBC 的 getGeneratedKeys（）方法获取并返回由数据库（像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段）内部产生的主键，此时，可以使用keyProperty指定主键字段。如果数据库不支持自动生成主键的字段，可以使用子标签\<selectKey>来实现。useGeneratedKeys也可以在mybatis的配置文件中进行全局设置

3. 自增主键回填演示

   ```xml
   <!--添加一个用户，成功后将主键值返回填给uid(po的属性)-->
   <insert id="addUser" parameterType="com.jzp.User" keyProperty="uid" useGeneratedKeys="true">
       insert into user (uname,usex) values(#{uname},#{usex})
   </insert>
   ```

   ```java
   // 添加一个用户
   User addmu = new User();
   addmu.setUname("张三");
   addmu.setUsex("男");
   int add = userDao.addUser(addmu);
   System.out.println("添加了" + add + "条记录");
   System.out.println("添加记录的主键是" + addmu.getUid());
   ```

4. 使用子标签\<selectKey>自定义主键：如果在实际工程中使用的数据库不支持主键自动递增（例如 Oracle），或者取消了主键自动递增的规则，可以使用 MyBatis 的 \<selectKey> 元素来自定义生成主键

   ```xml
   <!-- 添加一个用户，#{uname}为 com.jzp.User 的属性值 -->
   <insert id="insertUser" parameterType="com.jzp.User">
       <!-- 先使用selectKey元素定义主键，然后再定义SQL语句 -->
       <selectKey keyProperty="uid" resultType="Integer" order="BEFORE">
       select if(max(uid) is null,1,max(uid)+1) as newUid from user)
       </selectKey>
       insert into user (uid,uname,usex) values(#{uid},#{uname},#{usex})
   </insert>
   ```

   在执行上述示例代码时，\<selectKey> 元素首先被执行，该元素通过自定义的语句设置数据表的主键，然后执行插入语句。其中：

   - order 属性可以设置为 BEFORE 或 AFTER
   - BEFORE 表示先执行 \<selectKey> 元素然后执行插入语句
   - AFTER 表示先执行插入语句再执行 \<selectKey> 元素

## 4.3 update和delete

1. \<update>和\<delete>元素比较简单，它们的属性和 \<insert> 元素、\<select> 元素的属性差不多，执行后也返回一个整数，表示影响了数据库的记录行数

2. 演示

   ```xml
   <!-- 修改一个用户 -->
   <update id="updateUser" parameterType="com.jzp.User">
       update user set uname = #{uname},usex = #{usex} where uid = #{uid}
   </update>
   <!-- 删除一个用户 -->
   <delete id="deleteUser" parameterType="Integer">
       delete from user where uid = #{uid}
   </delete>
   ```

## 4.4 select

1. 根据id查询User

   ```xml
   <!--根据uid查询一个用户信息 -->
   <select id="selectUserById" parameterType="Integer" resultType="com.jzp.User">
       select * from user where uid = #{uid}
   </select>
   ```

2. 使用 Map 接口传递多个参数（数据需要通过map中的键来获取，使用 Map 不能限定其传递的数据类型）

   - UserDao

   ```java
   public List<User> selectAllUser(Map<String,Object> param);
   ```

   - UserController 

   ```java
   //查询多个用户
   Map<String,Object> map = new HashMap<>();
   map.put("u_name","陈");
   map.put("u_sex","男");
   List<User> list = userDao.seleceAllUser(map);
   for(User user : list) {
       System.out.println(user);
   }
   ```

   - UserDao.xml：输入参数必须与Map中的键名一致

   ```xml
   <!-- 查询陈姓男性用户信息 -->
   <select id="selectAllUser" resultType="com.jzp.User">
       select * from user
       where uname like concat('%',#{u_name},'%')
       and usex = #{u_sex}
   </select>
   ```

3. 使用javaBean传递多个参数

   - UserDao

   ```java
   public List<User> selectAllUser(User user);
   ```

   - UserController 

   ```java
   User user = new User();
   user.setName("jzp");
   user.setSex("男");
   List<MyUser> list = userDao.seleceAllUser(user);
   ```

   - UserDao.xml

   ```xml
   <select id="selectAllUser" resultType="com.jzp.User" parameterType="com.jzp.User">
       select * from user
       where namr like concat('%',#{name},'%')
       and sex=#{sex}
   </select>
   ```
   
4. 多参数注解传递

   - UserDao

   ```java
   public List<User> selectAllUser(@Param("name") String name,@Param("sex") String sex);
   ```

   - UserController 

   ```java
   List<MyUser> list = userDao.seleceAllUser("jzp","男");
   ```

   - UserDao.xml

   ```xml
   <select id="selectAllUser" resultType="com.jzp.User">
       select * from user
       where namr like concat('%',#{name},'%')
       and sex=#{sex}
   </select>
   ```

   

## 4.5 sql和include

1. \<sql> 元素的作用在于可以定义 SQL 语句的一部分（代码片段），以方便后面的 SQL 语句引用它，例如反复使用的列名。

2. \<include>可以通过refid属性来获取代码片段

3. 演示

   ```xml
   <sql id="comColumns">id,uname,usex</sql>
   <select id="selectUser" resultType="com.po.MyUser">
       select <include refid="comColumns" /> from user
   </select>
   ```


## 4.6 resultMap

1. \<resultMap> 元素表示结果映射集，是 MyBatis 中最重要也是最强大的元素，主要用来定义映射规则、级联的更新以及定义类型转化器等。

2. \<resultMap>包含了以下子元素：

   ```xml
   <resultMap id="" type="POJO">
       <constructor><!-- 类在实例化时用来注入结果到构造方法，当POJO未定义无参数的构造方法时使用 -->
           <idArg/><!-- ID参数，结果为ID -->
           <arg/><!-- 注入到构造方法的一个普通结果 -->  
       </constructor>
       <id/><!-- 用于表示哪个列是主键 -->
       <result/><!-- 注入到字段或JavaBean属性的普通结果，表示POJO和数据表普通列的映射关系，当实体属性与表字段名不同的时候，必须要有 -->
       <association property=""/><!-- 用于一对一关联 -->
       <collection property=""/><!-- 用于一对多、多对多关联 -->
       <discriminator javaType=""><!-- 使用结果值来决定使用哪个结果映射 -->
           <case value=""/><!-- 基于某些值的结果映射 -->
       </discriminator>
   </resultMap>
   ```

3. 使用\<resultMap>

   - UserDao.xml

   ```xml
   <!--使用自定义结果集类型-->
   <resultMap type="com.jzp.User" id="myResult">
       <!-- property 是 com.jzp.User 类中的属性-->
       <!-- column是查询结果的列名，可以来自不同的表-->
       <id property="id" column="uid"/>
       <result property="name" column="uname"/>
       <result property="sex" column="usex"/>
   </resultMap>
   
   <!-- 使用自定义结果集类型查询所有用户 -->
   <select id="selectResultMap" resultMap="myResult">
       select * from user
   </select>
   ```

## 4.7 存储过程

```xml
<select id="test" statementType="CALLABLE" parameterType="int" resultType="String">
    CALL te(#{value jdbcType=Integer,mode=IN})
</select>
```



# 五、级联

## 5.1 概念

1. 级联的优点是获取关联数据十分方便，但是级联过多会增加数据库系统的复杂度，同时降低系统的性能。
2. 一般级联都是指级联查询，而更新和删除的级联关系很简单，由数据库内在机制即可完成

3. 如果表 A 中有一个外键引用了表 B 的主键，A 表就是子表，B 表就是父表。当查询表 A 的数据时，通过表 A 的外键将表 B 的相关记录返回，这就是级联查询。例如，当查询一个人的信息时，同时根据外键（身份证号）将他的身份证信息返回。
4. MyBatis实现级联查询靠的就是\<resultMap>
5. 级联关系时一个数据库实体的概念，有三种级联关系
   - 一对一级联
   - 一对多级联
   - 多对多级联

## 5.2 一对一级联

1. 实现一对一级联是通过 \<resultMap> 元素的子元素 \<association> 处理这种一对一级联关系

2. 在 \<association> 元素中通常使用以下属性:

   - property：指定映射到实体类的对象属性。
   - column：指定表中对应的字段（即查询返回的列名）。
   - javaType：指定映射到实体对象属性的类型。
   - select：指定引入嵌套查询的子 SQL 语句，该属性用于关联映射中的嵌套查询。

3. 根据id查询个人信息和身份证信息，两个表（身份证表和个人信息表）

   - 身份证表

   ```java
   public class Idcard {
       private Integer id;
       private String code;
   }
   ```

   - 个人信息表（能和身份证信息关联）

   ```java
   public class Person {
       private Integer id;
       private String name;
       private Integer age;
       // 个人身份证关联
       private Idcard card;
   }
   ```

4. 执行两个SQL语句的查询

   ```xml
   <!-- 一对一根据id查询个人信息：级联查询的第一种方法（嵌套查询，执行两个SQL语句）-->
   <select id="selectCodeById" parameterType="Integer" resultType= "com.po.Idcard">
       select * from idcard where id=#{id}
   </select>
   <resultMap type="com.jzp.Person" id="cardAndPerson1">
       <id property="id" column="id"/>
       <result property="name" column="name"/>
       <result property="age" column="age"/>
       <!-- 一对一级联查询-->
       <association property="card" column="idcard_id" javaType="com.jzp.Idcard"
                    select="com.dao.IdCardDao.selectCodeByld"/>
   </resultMap>
   <select id="selectPersonById1" parameterType="Integer" resultMap=
           "cardAndPerson1">
       select * from person where id=#{id}
   </select>
   ```

5. 执行一个SQL语句的查询

   ```xml
   <!--对一根据id查询个人信息：级联查询的第二种方法（嵌套结果，执行一个SQL语句）-->
   <resultMap type="com.po.Person" id="cardAndPerson2">
       <id property="id" column="id"/>
       <result property="name" column="name"/>
       <result property="age" column="age"/>
       <!-- 一对一级联查询-->
       <association property="card" javaType="com.po.Idcard">
           <id property="id" column="idcard_id"/>
           <result property="code" column="code"/>
       </association>
   </resultMap>
   <select id="selectPersonById2" parameterType="Integer" resultMap= "cardAndPerson2">
       select p.*,ic.code
       from person p, idcard ic
       where p.idcard_id=ic.id and p.id=#{id}
   </select>
   ```

6. 使用POJO存储

   ```xml
   <!-- 一对一根据id查询个人信息：连接查询（使用POJO存储结果）-->
   <select id="selectPersonById3" parameterType="Integer" resultType= "com.pojo.SelectPersonById">
       select p.*,ic.code
       from person p, idcard ic
       where p.idcard_id = ic.id and p.id=#{id}
   </select>
   ```

## 5.3 一对多级联

1. 一个用户有多个订单

2. 订单表

   ```java
   public class Orders {
       private Integer id;
       private String ordersn;
   }
   ```

3. 嵌套查询

   ```xml
   <!-- 一对多 根据uid查询用户及其关联的订单信息：级联查询的第一种方法（嵌套查询） -->
   <resultMap type="com.jzp.User" id="userAndOrders1">
       <id property="uid" column="uid" />
       <result property="uname" column="uname" />
       <result property="usex" column="usex" />
       <!-- 一对多级联查询，ofType表示集合中的元素类型，将uid传递给selectOrdersByld -->
       <collection property="ordersList" ofType="com.po.Orders"
                   column="uid" select="com.dao.OrdersDao.selectOrdersByld" />
   </resultMap>
   <select id="selectUserOrdersById1" parameterType="Integer"
           resultMap="userAndOrders1">
       select * from user where uid = #{id}
   </select>
   ```

4. 嵌套结果

   ```xml
   <!--对多根据uid查询用户及其关联的订单信息：级联查询的第二种方法（嵌套结果） -->
   <resultMap type="com.jzp.User" id="userAndOrders2">
       <id property="uid" column="uid" />
       <result property="uname" column="uname" />
       <result property="usex" column="usex" />
       <!-- 对多级联查询，ofType表示集合中的元素类型 -->
       <collection property="ordersList" ofType="com.po.Orders">
           <id property="id" column="id" />
           <result property="ordersn" column="ordersn" />
       </collection>
   </resultMap>
   <select id="selectUserOrdersById2" parameterType="Integer"
           resultMap="userAndOrders2">
       select u.*,o.id, o.ordersn from user u, orders o where u.uid
       = o.user_id and
       u.uid=#{id}
   </select>
   ```

5. 使用POJO存储

   ```xml
   <!-- 一对多 根据uid查询用户及其关联的订单信息：连接查询（使用POJO存储结果） -->
   <select id="selectUserOrdersById3" parameterType="Integer"
           resultType="com.pojo.SelectUserOrdersById">
       select u.*, o.id, o.ordersn from user u, orders o where
       u.uid = o.user_id
       and u.uid=#{id}
   </select>
   ```

## 5.4 多对多关联

1. MyBatis 没有实现多对多级联，这是因为多对多级联可以通过两个一对多级联进行替换
2. 所有订单以及每个订单对应的商品信息

## 5.5 延迟加载

1. 如果不采用延迟加载 （立即加载），查询时会将一和多 都查询，班级、班级中的所有学生。
   如果想要  暂时只查询1的一方，  而多的一方 先不查询 而是在需要的时候再去查询 -->延迟加载

2. 使用MyBatis的延迟加载可以在一定程度上提高查询效率

3. mybatis中使用延迟加载，需要先配置：

   ```xml
   <settings>
       <!-- 开启延迟加载 -->
       <setting name="lazyLoadingEnabled" value="true"/>
       <!-- 关闭立即加载，即将积极加载改为按需加载 -->
       <setting name="aggressiveLazyLoading" value="false"/>
   </settings>
   ```

   

# 六、动态SQL

## 6.1 概述

1.  MyBatis 提供了对 SQL 语句动态组装的功能
2. MyBatis 的动态 SQL 元素与 JSTL或 XML 文本处理器相似，主要有一下几类：
   - 控制动态SQL拼接：\<if>、\<foreach>、\<choose>
   - 格式化输出：\<where>\<set>\<trim>

## 6.2 if

1. 根据条件动态查询

   ```xml
   <!--使用 if 元素根据条件动态查询用户信息-->
   <select id="selectUserByIf" resultType="com.jzp.User" parameterType="com.jzp.User">
       select * from user where 1=1
       <if test="uname!=null and uname!=''">
           and uname like concat('%',#{uname},'%')
       </if >
       <if test="usex !=null and usex !=''">
           and usex=#{usex}
       </if >
   </select>
   ```

## 6.3 choose/when

1. 类似于Java中的switch

   ```xml
   <!--使用choose、when、otherwise元素根据条件动态查询用户信息-->
   <select id="selectUserByChoose" resultType="com.jzp.User" parameterType= "com.jzp.User">
       select * from user where 1=1
       <choose>
           <when test="uname!=null and uname!=''">
               and uname like concat('%',#{uname},'%')
           </when>
           <when test="usex!=null and usex!=''">
               and usex=#{usex}
           </when>
           <otherwise>
               and uid > 10
           </otherwise>
       </choose>
   </select>
   ```

## 6.4 trim/where/set

1. \<trim> 元素的主要功能是可以在自己包含的内容前加上某些前缀，也可以在其后加上某些后缀，与之对应的属性是 prefix 和 suffix。

2. 可以把包含内容的首部某些内容覆盖，即忽略，也可以把尾部的某些内容覆盖，对应的属性是 prefixOverrides 和 suffixOverrides。

   ```xml
   <!--使用trim元素根据条件动态查询用户信息-->
   <select id="selectUserByTrim" resultType="com.jzp.User"parameterType="com.jzp.User">
       select * from user
       <trim prefix="where" prefixOverrides = "and | or">
           <if test="uname!=null and uname!=''">
               and uname like concat('%',#{uname},'%')
           </if>
           <if test="usex!=null and usex!=''">
               and usex=#{usex}
           </if>
       </trim>
   </select>
   ```

3. where元素的作用是会在写入 \<where> 元素的地方输出一个 where 语句

4. 不需要考虑 \<where> 元素里面的条件输出是什么样子的，MyBatis 将智能处理

5. 如果所有的条件都不满足，那么 MyBatis 就会查出所有的记录，如果输出后是以 and 开头的，MyBatis 会把第一个 and|or 忽略

   ```xml
   <!--使用where元素根据条件动态查询用户信息-->
   <select id="selectUserByWhere" resultType="com.jzp.User" parameterType="com.jzp.User">
       select * from user
       <where>
           <if test="uname != null and uname ! = ''">
               and uname like concat('%',#{uname},'%')
           </if>
           <if test="usex != null and usex != '' ">
               and usex=#{usex}
           </if >
       </where>
   </select>
   ```

6. set用于动态更新列

   ```xml
   <!--使用set元素动态修改一个用户-->
   <update id="updateUserBySet" parameterType="com.jzp.User">     
       update user
       <set>
           <if test="uname!=null">uname=#{uname}</if>
           <if test="usex!=null">usex=#{usex}</if>
       </set>
       where uid=#{uid}
   </update>
   ```

## 6.5 foreach

1. \<foreach> 元素主要用在构建 in 条件中，它可以在 SQL 语句中迭代一个集合。

2. \<foreach> 元素的属性主要有 item、index、collection、open、separator、close。

   - item 表示集合中每一个元素进行迭代时的别名。
   - index 指定一个名字，用于表示在迭代过程中每次迭代到的位置。
   - open 表示该语句以什么开始。
   - separator 表示在每次进行迭代之间以什么符号作为分隔符。
   - close 表示以什么结束。

3. collection是必选的，有三种情况

   - 如果传入的是单参数且参数类型是一个 List，collection 属性值为 list。
   - 如果传入的是单参数且参数类型是一个 array 数组，collection 的属性值为 array。
   - 如果传入的参数是多个，需要把它们封装成一个 Map，当然单参数也可以封装成 Map。Map 的 key 是参数名，collection 属性值是传入的 List 或 array 对象在自己封装的 Map 中的 key。

4. 演示

   ```xml
   <!--使用foreach元素查询用户信息-->
   <select id="selectUserByForeach" resultType="com.po.MyUser" parameterType=
   "List">
       select * from user where uid in
       <foreach item="item" index="index" collection="list"
       open="(" separator="," close=")">
           # {item}
       </foreach>
   </select>
   ```

## 6.6 bind

1. 在进行模糊查询时，如果使用“${}”拼接字符串，则无法防止 SQL 注入问题。如果使用字符串拼接函数或连接符号，但不同数据库的拼接函数或连接符号不同。

2. 例如 MySQL的 concat 函数、Oracle 的连接符号“||”，这样 SQL 映射文件就需要根据不同的数据库提供不同的实现，显然比较麻烦，且不利于代码的移植

3. 演示

   ```xml
   <!--使用bind元素进行模糊查询-->
   <select id="selectUserByBind" resultType="com.jzp.User" parameterType= "com.jzp.User">
       <!-- bind 中的 uname 是 com.jzp.User 的属性名-->
       <bind name="paran_uname" value="'%' + uname + '%'"/>
           select * from user where uname like #{paran_uname}
   </select>
   ```



# 七、日志

1. 开启日志

```
<settings>
	<!-- 开启日志，并指定使用的具体日志 -->
	<setting name="logImpl" value="LOG4J"/>
</settings>
```

​	如果不指定，Mybatis就会根据以下顺序 寻找日志
​	SLF4J →Apache Commons Logging →Log4j 2 → Log4j →JDK logging

2. 日志 输出文件内容log4j.properties

```
log4j.rootLogger=DEBUG, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

​	日志级别：DEBUG<INFO<WARN<ERROR

​	在开发时设置debug，在运行时设置为info或以上。



# 八、缓存

## 8.1 概述

1. 缓存分为一级缓存和二级缓存，mybatis默认开启了一级缓存
2. 一级缓存：是针对同一个SqlSession而言的（参数和 SQL 完全一样的情况），第一次查询会将结果存放到SqlSession中，后续查询同样对象时则直接从缓存中查询该对象
3. 二级缓存：存在于 SqlSessionFactory 生命周期中，即将数据存入硬盘中
4. 如何判断是同一次查询（关乎cacheKey的构建）
   - 传入的statementId一致：对于MyBatis而言，你要使用它，必须需要一个statementId，它代表着你将执行什么样的Sql；
   - 要求的结果集中的结果范围一致：MyBatis自身提供的分页功能是通过RowBounds来实现的，它通过rowBounds.offset和rowBounds.limit来过滤查询出来的结果集，这种分页功能是基于查询结果的再过滤，而不是进行数据库的物理分页；
   - SQL语句字符串一致：即SQL语句一致，传入参数一致
5. 由于在更新时会刷新缓存， 因此需要注意使用场合：查询频率很高， 更新频率很低时使用， 即经常使用 select, 相对较少使用delete, insert,update。
6. 缓存是以 namespace 为单位的，不同 namespace 下的操作互不影响。但刷新缓存是刷新整个 namespace 的缓存， 也就是你 update 了一个， 则整个缓存都刷新了
7. 最好在 「只有单表操作」 的表的 namespace 使用缓存， 而且对该表的操作都在这个 namespace 中。 否则可能会出现数据不一致的情况。

## 8.2 一级缓存

1. 在同一个 `SqlSession` 中, Mybatis 会把执行的方法和参数通过算法生成缓存的键值， 将键值和结果存放在一个 Map 中， 如果后续的键值一样， 则直接从 Map 中获取数据；

2. 不同的 `SqlSession` 之间的缓存是相互隔离的；

3. 用一个 `SqlSession`， 可以通过配置使得在查询前清空缓存；

4. 任何的 UPDATE, INSERT, DELETE 语句都会清空缓存。

5. 在单条查询语句中指定flushCache=“true”可以刷新缓存

   ```xml
   <select id="selectUserById" parameterType="Integer" resultType="com.jzp.User" flushCache="true">
       select * from user where uid = #{uid}
   </select>
   ```

6. 一级缓存在单机环境下可以减少与 MySql 的交互，提高性能，但是在分布式环境下容易产生脏数据。建议在生产环境下关闭，使用 Redis，Memcache 等代替。

## 8.3 二级缓存

1. 二级缓存有两种开关，分别在MyBatis配置文件（全局开关）和映射文件（分开关）中

   - 全局开关：默认开启二级缓存，是一个总开关

     ```xml
     <settings>
       <!--全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。 -->
       <setting name="cacheEnabled" value="true"/>
     </settings>
     ```

   - 分开关：默认关闭，一般开启二级缓存都是指开启分开关

     ```xml
     <cache eviction="LRU" flushInterval="100000" readOnly="true" size="1024"/>
     ```

2. 二级缓存因为是保存在硬盘中，所以POJO需要序列化，即实现序列化接口（implements Serializable）
3. cache中有一些可选的属性，如下：
   - type：用于指定缓存的实现类型，默认是`PERPETUAL`，对应的是 mybatis 本身的缓存实现类`org.apache.ibatis.cache.impl.PerpetualCache`，如果我们要实现自己的缓存或者使用第三方的缓存， 都需要更改此处。比如要使用redis作为二级缓存
   - eviction：缓存回收策略，默认LRU，有以下属性值
     - LRU: 最近最少使用， 移除最长时间不被使用的对象。
     - FIFO: 先进先出， 按对象进入缓存的顺序来移除对象。
     - SOFT: 软引用， 移除基于垃圾回收器状态和软引用规则的对象。
     - WEAK: 弱引用， 移除基于垃圾回收器状态和弱引用规则的对象。
   - flushInterval：对应刷新间隔， 单位毫秒， 默认值不设置， 即没有刷新间隔， 缓存仅仅在刷新语句时刷新。如果设定了之后， 到了对应时间会过期， 再次查询需要从数据库中取数据。
   - size：对应为引用的数量，即最多的缓存对象数据， 默认为 1024。
   - readOnly：只读属性， 默认为 false，其中：
     - false：可读写， 在创建对象时， 会通过反序列化得到缓存对象的拷贝。 因此在速度上会相对慢一点， 但重在安全。
     - true：只读， 只读的缓存会给所有调用者返回缓存对象的相同实例。 因此性能很好， 但如果修改了对象， 有可能会导致程序出问题。
   - blocking：阻塞，默认值为false，当指定为 true 时将采用 `BlockingCache` 进行封装。使用 `BlockingCache` 会在查询缓存时锁住对应的 Key，如果缓存命中了则会释放对应的锁，否则会在查询数据库以后再释放锁，这样可以阻止并发情况下多个线程同时查询数据。

# 九、数据源

1. mysql数据源

   ```xml
   <!-- 配置数据源 --> 
   <bean id="dataSource" class="org.springframework.jdbc.dataSource.DriverManagerDataSource">
       <!--数据库驱动-->
       <property name="driverClassName" value="com.mysql.jdbc.Driver" /> 
       <!--连接数据库的url-->
       <property name= "url" value="jdbc:mysql://localhost/spring" />
       <!--连接数据库的用户名-->
       <property name="username" value="root" />
       <!--连接数据库的密码-->
       <property name="password" value="root" />
   </bean>
   <!--配置JDBC模板-->
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.jdbcTemplate">
       <!--默认必须使用数据源-->
       <property name="dataSource" ref="dataSource"/>
   </bean>
   ```

2. dbcp数据源

   ```xml
   <!--配置数据源 -->
   <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
         destroy-method="close" scope="singleton">
       <property name="driverClassName" value="com.mysql.jdbc.Driver" />
       <property name="url"
                 value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8" />
       <property name="username" value="root" />
       <property name="password" value="1128" />
       <property name="maxTotal" value="30" />
       <property name="maxIdle" value="10" />
       <property name="initialSize" value="5" />
   </bean>
   
   ```

3. c3p0数据源

   ```xml
   <!-- 配置数据源，读取properties文件信息 -->
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
       <property name="driverClass" value="${jdbc.driverClass}" />
       <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />
       <property name="user" value="${jdbc.user}" />
       <property name="password" value="${jdbc.password}" />
       <property name="initialPoolSize" value="${db.initialPoolSize}"/> <!-- 默认5-->
       <property name="maxPoolSize" value="${db.maxPoolSize}"/> <!-- 默认10-->
       <property name="checkoutTimeout" value="${db.checkoutTimeout}"/> <!-- 默认3000-->
   </bean>
   ```

4. jdbc模板

   ```xml
   <!-- 配置jdbc模板 -->
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
       <property name="dataSource" ref="dataSource" />
   </bean>
   ```

5. mybitas工厂模板

   ```xml
   <!-- 配置mybitas工厂，同时指定数据源，并与MyBatis完美整合 -->
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <property name="dataSource" ref="dataSource" />
       <!-- configLocation的属性值为Mybatis的核心配置文件 -->
       <property name="configLocation" value="classpath:mybatis-config.xml" />
   </bean>
   ```

6. 加载properties文件

   ```xml
   <!-- 加载properties文件 -->
   <context:property-placeholder location="classpath:c3p0-db.properties" />
   ```

