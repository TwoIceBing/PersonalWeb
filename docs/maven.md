# Maven

# 一. 概念

1. maven是一个项目管理工具，包含了一个项目对象模型（POM：project object model），一组标准集合，一个项目生命周期，一个依赖管理系统，和用来运行定义在生命周期阶段中插件目标的逻辑。
2. maven能构建工程，管理jar包，编译代码，自动运行单元测试，打包，生成报表，部署项目生成Web站点。
3. 不仅仅是Java，maven还能管理C#，Ruby，Scala和其他语言编写的项目
4. 仓库种类：
   1. 本地仓库：jar放在自己的计算机上
   2. 远程仓库：搭建的远程仓库
   3. 中央仓库：默认的远程下载仓库，几乎有所有的jar包
5. 本地仓库路径：在conf/setting.xml文件的\<localRepository>标签中

# 二、POM

1. pom是一个XML文件，包含了项目的基本信息，用于描述项目如何构建，声明项目依赖，等等。 

2. 执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。 

3. maven最常用的字段：
   1.  groupId： 工程组的标识。它在一个组织或者项目中通常是唯一的
   2.  artifactId： 工程的标识。通常是工程的名称
   3.  version： 工程的版本号
   
4. 版本统一：

   ```xml
   <properties>
       <spring.version>5.0.2.RELEASE</spring.version>
   </properties>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-core</artifactId>
       <version>${spring.version}</version>
   </dependency>
   ```

   

5. 配置示例

   * 头文件

   ```java
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   </project>
   ```

   - 必须的配置

   ```java
   <groupId>groupId</groupId>
   <artifactId>artifactId</artifactId>
   <version>1.0-SNAPSHOT</version>
   <packaging>jar/war</packaging>
   ```

   

# 三、项目的使用

## 3.1 主属性

```xml
<groupId>com.jzp</groupId>
<artifactId>SpringTest</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>war</packaging>
```

## 3.2 properties

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
</properties>
```

## 3.3 Spring基本依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 3.4 jdbcTemplate依赖

事务支持和jdbcTemplate

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 3.5 Servlet和jsp依赖

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2</version>
    <scope>provided</scope>
</dependency>
```

## 3.6 mysql依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.5</version>
</dependency>
```

## 3.7 c3p0依赖

```xml
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.2</version>
</dependency>
```

## 3.8 cglib依赖

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.2.5</version>
</dependency>
```

## 3.9 AspectJ依赖

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.1</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.1</version>
</dependency>
```

## 3.10 Junit依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
</dependency>
```

## 3.11 log4j依赖

```xml
<!-- Spring5以上不用这个，用下面三个 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.12</version>
</dependency>

<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.10.0</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.10.0</version>
</dependency>

<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-web</artifactId>
    <version>2.9.1</version>
</dependency>
```

## 3.12 mybatis依赖

事务注解

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.2</version>
</dependency>
```

## 3.13 json依赖

阿里巴巴json

```xml
<dependency>  
    <groupId>com.alibaba</groupId>  
    <artifactId>fastjson</artifactId>  
    <version>1.2.62</version>  
</dependency> 
```

net.sf.json

```xml
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier>
</dependency>
```

jackjson

```xml
<jackjson.version>2.8.8</jackjson.version>

<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-core</artifactId>
   <version>${jackjson.version}</version>
</dependency>
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-annotations</artifactId>
   <version>${jackjson.version}</version>
</dependency>
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>${jackjson.version}</version>
</dependency>
```



## 3.14 Spring MVC依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 3.15 JSTl依赖

```xml
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>taglibs</groupId>
    <artifactId>standard</artifactId>
    <version>1.1.2</version>
</dependency>
```



