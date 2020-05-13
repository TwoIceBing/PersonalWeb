# SpringMVC

# 一、基础

## 1.1 概述

1. SpringMVC是基于Java的实现MVC设计模型的请求驱动类型的轻量级Web框架，它通过一套注解，让一个简单的Java类成为处理请求的控制器，而无须实现任何借口，同时还支持RESTful编程风格的请求。
2. MVC 是 Model、View 和 Controller 的缩写，分别代表 Web 应用程序中的 3 种职责。
   - 模型：用于存储数据以及处理用户请求的业务逻辑。
   - 视图：向控制器提交数据，显示模型中的数据。
   - 控制器：根据视图提出的请求判断将请求和数据交给哪个模型处理，将处理后的有关结果交给哪个视图更新显示。
3. 基于 Servlet的 MVC 模式的具体实现如下。
   - 模型：一个或多个 JavaBean 对象，用于存储数据（实体模型，由 JavaBean 类创建）和处理业务逻辑（业务模型，由一般的 Java 类创建）。
   - 视图：一个或多个JSP页面，向控制器提交数据和为模型提供数据显示，JSP 页面主要使用 HTML 标记和 JavaBean 标记来显示数据。
   - 控制器：一个或多个 Servlet 对象，根据视图提交的请求进行控制，即将请求转发给处理业务逻辑的 JavaBean，并将处理结果存放到实体模型 JavaBean 中，输出给视图显示。

## 1.2 SpringMVC和Struts2的对比

1. 共同点：

   - 都是表现层框架，都是基于MVC模型编写的
   - 底层都离不开ServletAPI 
   - 处理请求的机制都是一个核心控制器
2. 区别：
   - SpringMVC的入口是Servlet，而Struts2是Filter	
   - SpringMVC是基于方法设计的，Struts2是基于类，所以SpringMVC快一点
   - SpringMVC使用更加简洁，同时支持JSR303，处理ajax请求更加方便

## 1.3 工作流程

1. Spring MVC 框架主要由 DispatcherServlet、处理器映射、控制器、视图解析器、视图组成，其工作原理如图 所示：![](./image/5-1ZG2095404c8.png)
2. 总结：
   - 客户端请求提交到 DispatcherServlet。
   - 由 DispatcherServlet 控制器寻找一个或多个 HandlerMapping，找到处理请求的 Controller。
   - DispatcherServlet 将请求提交到 Controller。
   - Controller 调用业务逻辑处理后返回 ModelAndView给DispatcherServlet。
   - DispatcherServlet 寻找一个或多个 ViewResolver 视图解析器，找到 ModelAndView 指定的视图。
   - 视图负责将结果显示到客户端。

## 1.4 Spring MVC主接口

1. Spring MVC有以下4个接口，即DispatcherServlet、HandlerMapping、Controller 和 ViewResolver
   - DispatcherServlet（前端控制器）：Spring MVC 所有的请求都经过 DispatcherServlet 来统一分发，在 DispatcherServlet 将请求分发给 Controller 之前需要借助 Spring MVC 提供的 HandlerMapping 定位到具体的 Controller。
   - HandlerMapping（处理器映射器） ：负责完成客户请求到 Controller 映射。
   - Controller ：将处理用户请求，这和 JavaServlet 扮演的角色是一致的。一旦 Controller 处理完用户请求，将返回 ModelAndView 对象给 DispatcherServlet 前端控制器，ModelAndView 中包含了模型（Model）和视图（View）。
   - ViewResolver（视图解析器） ：在 Web 应用中负责查找 View 对象，从而将相应结果渲染给客户。
2. 从宏观角度考虑，DispatcherServlet 是整个 Web 应用的控制器；从微观考虑，Controller 是单个 Http 请求处理过程中的控制器，而 ModelAndView 是 Http 请求过程中返回的模型（Model）和视图（View）。

## 1.5 maven依赖

1. Spring依赖

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>${spring.version}</version>
   </dependency>
   ```

2. Spring MVC依赖

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

3. Servlet和jsp依赖

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
   
4. Hibernate Validator依赖

   ```xml
   <dependency>
       <groupId>org.hibernate</groupId>
       <artifactId>hibernate-validator</artifactId>
       <version>5.4.1.Final</version>
   </dependency>
   ```

5. 文件上传依赖

   ```xml
   <dependency>  
       <groupId>commons-fileupload</groupId>  
       <artifactId>commons-fileupload</artifactId>  
       <version>1.3.1</version>  
   </dependency>  
   <dependency>  
       <groupId>commons-io</groupId>  
       <artifactId>commons-io</artifactId>  
       <version>2.4</version>  
   </dependency>  
   ```

   


## 1.6 Spring MVC配置

1. 前端控制器（web.xml）

   ```xml
   <servlet>
       <!-- 配置前端控制器 -->
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!-- 加载springmvc的配置 -->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <!-- 启动服务器时加载Servlet -->
       <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <!-- 任何请求都会经过此Servlet -->
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   
   <!-- ContextLoaderListener监听器，一般用于自动装配ApplicationContext的配置信息 -->
   <listener>
       <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
   ```
   
2. springmvc注解文件的一些配置（springmvc-servlet.xml）

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!-- 开启注解扫描 -->
       <context:component-scan base-package="controller"/>
       <context:component-scan base-package="validator"/>
   
       <!-- 视图解析器对象 -->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   
       <!-- 开启SpringMVC框架注解的支持 -->
       <!-- 简化配置： 
   		(1)自动注册DefaultAnootationHandlerMapping,AnotationMethodHandlerAdapter 
   		(2)提供一些列：数据绑定，数字和日期的format @NumberFormat, @DateTimeFormat, xml,json默认读写支持 
   	-->
       <mvc:annotation-driven/>
       
       <!-- 2.静态资源默认servlet配置
   		(1)加入对静态资源的处理：js,gif,png
   		(2)允许使用"/"做整体映射
   	 -->
   	 <mvc:default-servlet-handler/>
       
       <!-- 允许static目录下的所有文件可见
    		使用resources过滤掉不需要dispatcherservlet的资源（即静态资源，例如css、js、html、		images）。
           在使用resources时必须使用annotation-driven，否则resources元素会阻止任意控制器被调用
   	-->
       <mvc:resources location="/static/" mapping="/static/**" />
       
       <!-- 配置消息属性文件 -->
       <!-- 一般用于验证器 -->
       <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
           <!-- 如果只有一个消息属性文件，就可以不用list标签 -->
           <!-- <property name="basenames" value="message"/> -->
           <property name="basenames">
               <list>
                   <value>message</value>
               </list>
           </property>
           <!-- 资源文件编码格式 -->
           <property name="fileEncodings" value="utf-8" />
           <!-- 对资源文件内容缓存的时间，单位为秒 -->
           <property name="cacheSeconds" value="120" />
       </bean>
       
       <!-- 注册hibernate校验器 -->
   <bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
       <!-- hibernate 校验器 -->
       <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
       <!-- 指定校验使用的资源文件，在文件中配置校验错误信息，如果不指定则默认使用 classpath下的 ValidationMessages.properties -->
       <property name="validationMessageSource" ref="messageSource" />
   </bean>
   <!--开启 Spring的 Valid 功能（Springmvc-validator不需要这个） -->
   <mvc:annotation-driven conversion-service="conversionService" validator="validator" />
   </beans>
   ```

3. 中文乱码

   ```xml
   <filter>
       <filter-name>characterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>utf-8</param-value>
       </init-param>
       <init-param>
               <param-name>forceEncoding</param-name>
               <param-value>true</param-value>
           </init-param>
   </filter>
   <filter-mapping>
       <filter-name>characterEncodingFilter</filter-name>
       <url-pattern>/</url-pattern>
   </filter-mapping>
   ```

# 二、组件

## 2.1 视图解析器

1. 视图解析器（ViewResolver）是SpringMVC的重要组成部分，在实际应用中，InternalResourceViewResolver是使用的最广泛的一个视图解析器，InternalResourceViewResolver 是 URLBasedViewResolver 的子类，所以 URLBasedViewResolver 支持的特性它都支持。

2. InternalResourceViewResolver即内部资源视图解析器，它会会把返回的视图名称都解析为 InternalResourceView 对象，InternalResourceView 会把 Controller 处理器方法返回的模型属性都存放到对应的 request 属性中，然后通过 RequestDispatcher 在服务器端把请求 forword 重定向到目标 URL。

3. 为了安全性考虑，通常会把 jsp 文件放在 WEB-INF 目录下，因为 /WEB-INF/ 下面的内容是不能直接通过 request 请求的方式请求到的

4. 配置视图解析器（springmvc-servlet.xml）

   ```xml
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" >
       <!--前缀-->
       <property name="prefix" value="/WEB-INF/jsp/"/>
       <!--后缀-->
       <property name="suffix" value=".jsp"/>
   </bean>
   ```

   - 相当于路径/WEB-INF/jsp/*.jsp路径的自动扫描

## 2.2 前端控制器

1. 前端控制器（DispatcherServlet），是Spring MVC的集中访问点，主要职责是调度、流程控制。

2. 前端控制器负责接收用户的请求并根据用户的请求返回相应的视图给用户。 

3. DispatcherServle继承继承了HttpServlet这个抽象类，一般要在web.xml中为整个Web项目配置一个前端控制器

   ```xml
   <servlet>
       <!-- 配置前端控制器 -->
       <servlet-name>springmvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
       <servlet-name>springmvc</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

   - 初始化参数一般会设置一个SpringMVC的配置文件路径，默认会去/WEB-INF/servletName-servlet.xml路径下寻找，例如上述配置文件就应该是/WEB-INF/springmvc-servlet.xml，SpringMVC规定，一个DispatcherServlet就应该有一个配置文件
   - load-on-startup是Servlet web.xml文件的一个标签，不独属于SpringMVC，在这里，表示容器在启动时就加载DispatcherServlet。它一般用来指定Servlet的加载顺序，数值越低，Servlet的优先级越高；当为1时，表示容器在启动时就加载Servlet；为0或大于0时，在应用启动时就加载这个servlet；是一个负数时或者没有指定时，则表示容器在该servlet被选择时才加载
   - 拦截路径：
     - *.jsp：拦截所有的jsp请求
     - /：拦截所有URL，但是不包括.jsp
     - /*：拦截所有URL，包括.jsp，不建议使用

4. 访问静态资源时，也会被前端控制器拦截，此时需要在配置文件中申明不拦截静态资源，如：

   ```xml
   <mvc:resources mapping="/static/**" location="/static/"/>
   ```

   

# 三、请求映射

## 3.1 Controller

1. 控制器（Controller）用于处理前端发过来的请求，部署一个控制器有两种方式：
   - 传统风格的控制器
   - 基于注解的控制器

### 3.1.1 传统风格的控制器

1. 传统风格的控制器不仅需要在配置文件中部署映射，而且只能编写一个处理方法，不够灵活。创建一个传统风格的控制器需要以下步骤

   - 创建控制器类，实现Controller接口

   ```java
   package controller;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   
   public class RegisterController implements Controller {
       public ModelAndView handleRequest(HttpServletRequest arg0,
               HttpServletResponse arg1) throws Exception {
           return new ModelAndView("/WEB-INF/jsp/login.jsp");
       }
   }
   ```

   - 在SpringMVC的配置文件中部署映射

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <!-- LoginController控制器类，映射到"/login" -->   
       <bean name="/login" class="controller.LoginController"/>
   </beans>
   ```

### 3.1.2 基于注解的控制器

1. 基于注解的控制器中可以编写多个处理方法，进而可以处理多个请求（动作），这就允许将相关的操作编写在同一个控制器类中，从而减少控制器类的数量，方便以后的维护；基于注解的控制器不需要在配置文件中部署映射，仅需要使用 RequestMapping 注释类型注解一个方法进行请求处理

2. 创建基于注解的控制器需要两个注解：

   - @Controller：用于申明类为控制器类
   - @RequestMapping：接受相应的处理请求

3. 创建一个Controller需要两个步骤，如下：

   - 创建一个Controller类，并加上@Controller注解

   ```java
   package controller;
   import org.springframework.stereotype.Controller;
   /**
   * “@Controller”表示 IndexController 的实例是一个控制器
   *
   * @Controller相当于@Controller(@Controller) 或@Controller(value="@Controller")
   */
   @Controller
   public class IndexController {
       // 处理请求的方法
   }
   ```

   - 让控制器类被 Spring MVC 框架扫描到，需要在SpringMVC的配置文件中使用 <context：component-scan/> 元素指定控制器类的基本包，并确保所有控制器类都在基本包及其子包下

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:p="http://www.springframework.org/schema/p" 
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <!-- 使用扫描机制扫描控制器类，控制器类都在controller包及其子包下 -->
       <context:component-scan base-package="controller" />
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/" />
           <property name="suffix" value=".jsp" />
       </bean>
   </beans>
   ```

### 3.1.3 前端访问

1. 前端访问控制器的方式一般有四种，即

   ```jsp
   <a href="${pageContext.request.contextPath }/login">注册</a>
   <a href="login">注册</a>
   <a href="/login">注册</a>
   <a href="http://localhost:8080/springMVCProject/login">注册</a>
   ```

## 3.2 RequestMapping

1. RequestMapping注解用于处理前端传递过来的URL

2. RequestMapping注解映射有两种

   - 类映射：能够拦截整个类的映射，一般用来申明某个模块
   - 函数映射：拦截某个请求

3. 如有以下Controller

   ```java
   package controller;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   @RequestMapping(value = "/index")
   public class IndexController {
       @RequestMapping(value = "/login")
       //@RequestMapping("/login") //和上面的一样
       public String login() {
           /**
            * login代表逻辑视图名称，需要根据Spring MVC配置
            * 文件中internalResourceViewResolver的前缀和后缀找到对应的物理视图
            */
           return "login";
       }
   }
   ```

   - IndexController类会拦截来自于http://localhost:8080/index下的所有请求
   - login函数会处理来自于http://localhost:8080/index/login的请求，并且根据Springmvc配置文件中的视图解析器返回对应的login.jps视图

4. RequestMapping的参数解释

   - path：指定映射路径，默认参数
   - value：相当于path参数
   - method：指定请求方式，如：`mathod={RequestMethod.POST}`
   - params：指定请求参数，如：
     - `params={"name"}`，请求的参数中必须有name
     - `params={"name=jzp"}`，请求参数中的name值必须为jzp
     - `params={"name!jzp"}`，请求参数中的name值不能为jzp
   - headers：指定限制请求消息头的条件，如：
     - `headers={"Accept"}`，请求头中必须有Accept
   
5. 如果存在多个RequestMapping指向同一个映射路径，会报IllegalStateException异常

6. Ant 风格的 URL 映射

   - `?`：匹配任何单字符
   - `*`：匹配任意数量的字符（含 0 个）
   - `**`：匹配任意数量的目录（含 0 个）

## 3.3 请求函数参数绑定

1. 一个映射函数的请求参数有很多中，如：
   - 普通参数
   - Bean参数
   - List参数
   - Map参数
   - Model参数
   - ModelAndView
   - Servlet原生API

### 3.3.1 普通参数

 1. 函数参数名必须多于请求参数名，没有的函数参数值会被初始化为null

    ```java
    @RequestMapping(path = "/login")
    public String login(String name,String password){
        System.out.println(name+":"+password);
    	return "login";
    }
    ```

    ```jsp
    <a href="user/login?name=jzp&password=123">注册</a>
    <br/>
    <a href="user/login?name=jzp">测试</a>
    ```

    ```console
    jzp:123
    jzp:null
    ```

### 3.3.2 Bean参数

1. POJO中的属性必须多于请求参数

   ```java
   @RequestMapping(path = "/login")
   public String login(User user){
       System.out.println(user.getName()+":"+user.getPassword());
       System.out.println(user.getAward().getCount());
   	return null;
   }
   ```

   ```java
   public class User{
   	private String name;
       private String password;
   	private Award award;
   	//get和set方法。。。
   }
   public class Award{
       private Stirng name;
       private String count;
       //get和set方法。。。
   }
   ```

   ```jsp
   <a href="user/login?name=jzp&password=123&award.name=aaa&award.count=3">注册</a>
   ```

### 3.3.3 list参数

1. 一般用于批量提交

   ```java
   @RequestMapping(path = "/login")
   public String login(List<User> list) {
       for (User user : list) {
           System.out.println(user.getName()+":"+user.getPassword());
       }
       return "login";
   }
   ```

   ```jsp
   <a href="user/login?[{"name":"karsa","password":"123"},{"name":"knight","password":"332"}]">注册</a>
   ```

2. 具体使用

   ```jsp
   <input type="text" name="list[0].name"><br>
   <input type="text" name="list[0].password"><br>
   <input type="text" name="list[1].name"><br>
   <input type="text" name="list[1].password"><br>
   ```

### 3.3.4 map参数

 1. 类似于list参数

    ```java
    @RequestMapping(path = "/login")
    public String login(Map<String,User> map) {
        System.out.println(map.get("mid").getName()+":"+map.get("mid").getPassword());
        System.out.println(map.get("jun").getName()+":"+map.get("jun").getPassword());
        return "login";
    }
    ```

    ```jsp
    <a href="user/login?{"mid":{"name":"knight","pwd":"332"},"jun":{"name":"karsa","pwd":"123"}}">注册</a>
    ```

	2. 具体使用

    ```jsp
    <input type="text" name="map['mid'].name"><br>
    <input type="text" name="map['mid'].password"><br>
    <input type="text" name="map['jun'].name"><br>
    <input type="text" name="map['jun'].password"><br>
    ```

### 3.3.5 Model参数

1. 一个Model相当于一个request域，每次请求都会自动携带

   ```java
   @RequestMapping(path = "/login")
   public String login(Model model){
       model.addAttribute("name","jzp")
       return "login";
   }
   ```

### 3.3.6 ModelAndView

1. 功能和Model差不多，Model实际上就是由ModelAndView实现的，不过ModelAndView是SpringMVC自带的类，可以直接实例化，这样就不需要通过参数来引入Model了

2. ModelAndView比Model多了个View，这意味着ModelAndView可以携带视图

   ```java
   @RequestMapping(path = "/login")
   public ModelAndView login(){
       ModelAndView mav = new ModelAndView();
       mav.addObject("name","jzp");
       //页面转发,将会转发到login.jsp页面
       mav.setViewName("login");
       return mav;
   }
   ```

### 3.3.7 Servlet原生API

1. 可以直接将Servlet原生API作为参数传递

   ```java
   @RequestMapping(path = "/login")
   public String login(HttpServletRequest req, HttpServletResponse resp){
   	HttpSession session = req.getSession();
       return "login";
   }
   ```

   

## 3.4 响应

1. 经过处理函数处理完成后，自然需要响应相应视图，一般有以下几种相应

   - 无返回值
   - 返回null
   - 返回字符串

   - 返回ModelAndView：这个在上面有解释
   - 返回json
   - 转发和重定向

### 3.4.1 无返回值

1. 如果RequestMapping的注解函数没有返回值，他会根据path的值去寻找jsp页面

2. 如：

   ```java
   @RequestMapping("/login")
   public void login(){
       System.out.println("login");
   }
   ```

3. 他会根据视图解析器跳转到login.jsp页面

### 3.4.2 返回null

1. 类似于无返回值函数，也是根据path的值去寻找jsp页面

2. 如：

   ```java
   @RequestMapping("/login")
   public String login(){
       System.out.println("login");
       return null;
   }
   ```

### 3.4.3 返回字符串

1. 如果RequestMapping的注解函数返回值是字符串，那么他会从视图解析器中的配置文件中找到相应的jsp文件

2. 如：

   ```java
   @RequestMapping("/login")
   public String login(){
       System.out.println("login");
       return "login";
   }
   ```

3. 他会根据视图解析器跳转到login.jsp页面

### 3.4.4 返回json

1. 其实直接返回一个实体类就是一个json

2. 如：

   ```java
   @RequestMapping("/login")
   public User login(User user){
       user.setName("jz");
       user.setAge(32);
       return user;
   }
   ```

### 3.4.5 返回普通字符串

1. 返回String时，如果不加注解@ResponseBody，表示返回的是个页面；如果加了该注解，表示返回的真的是一个字符串

2. 如：

   ```java
   @RequestMapping("/login")
   @ResponseBody
   public User login(){
       return "你好";
   }
   ```

   

### 3.4.5 转发和重定向

1. 转发和重定向是通过关键字来实现的

   - forward
   - redirect

2. 转发或重定向不会被视图解析器解析，都是创建一个新的请求，之后再在通过字符串相应

3. 如：

   ```java
   @RequestMapping("/login")
   public String login(){
       System.out.println("login");
       return "forward:/login2";
   }
   
   @RequestMapping("/login1")
   public String login1(){
       System.out.println("login1");
       return "redirect:/login2"
   }
   
   @RequestMapping("/login2")
   public String login2(){
       return "login"
   }
   ```

4. 也可以写绝对路径

   ```java
   @RequestMapping("/login")
   public String login(){
       System.out.println("login");
       return "forward:/WEB-INF/jsp/login.jsp";
   }
   ```

## 3.5 参数注解

1. 参数注解即给参数施加的注解，有以下几种
   - RequestParam
   - RequestBody
   - PathVariable
   - RequestHeader
   - CookieValue

### 3.5.1 RequestParam

1. 作用：把请求中指定名称的参数给控制器中的形参赋值；当请求参数与接收参数名不一致时，虽然接受不到参数，但也不会报404错误，而通过 @RequestParam 接收请求参数会报 404 错误。

2. 属性：

   - name或value：请求参数中的名称
   - required：是否必须提供此参数，默认true

3. 如：

   ```java
   @RequestMapping("/login")
   public String login(@RequestParam String username){
       System.out.println(username);
       return "login";
   }
   ```

   ```jsp
   <a href="login?username=jzp">注册</a>
   ```

4. 或

   ```java
   @RequestMapping("/login")
   public String login(@RequestParam(name="uname") String username){
       System.out.println(username);
       return "login";
   }
   ```

   ```jsp
   <a href="login?uname=jzp">注册</a>
   ```

### 3.5.2  RequestBody

1. 作用：获取请求体内容，得到的是key=value&...；get请求方式不适用

2. 属性：

   - required：是否必须有请求体，默认true；当为true时，get请求会报错，false时会得到null

3. 如：

   ```java
   @RequestMapping("/login")
   public String login(@RequestBody String body){
       System.out.println(body);
       return "login";
   }
   ```

### 3.5.3 PathVariable

1. 作用：用于绑定url中的占位符，spring3.0之后加入的，是springmvc支持restful的重要标志

2. 属性：

   - name或value：用于指定url中的占位符名称
   - required：是否必须提供占位符

3. 如

   ```java
   @RequestMapping("/login/{sid}")
   public String login(@PathVariable(name="sid") String id){
       System.out.println(id);
       return "login";
   }
   ```

   ```jsp
   <a href="login/10">注册</a>
   ```

4. 也可以不用{}

   ```java
   @RequestMapping("/login")
   public String login(@PathVariable(name="sid") String id,@PathVariable String name){
       System.out.println(id+":"+name);
       return "login";
   }
   ```

   ```jsp
   <a href="login/10/jzp">注册</a>
   ```


### 3.5.4 requestHeader

1. 作用：用于获取请求消息头，一般不用

2. 属性：

   - name或value：提供消息头名称
   - required：是否必须有此消息头

3. 如：

   ```java
   @RequestMapping("/login")
   public String login(@RequestHeader(name="Accept") String accept){
       System.out.println(accept);
       return "login";
   }
   ```

### 3.5.5 CookieValue

1. 作用：把指定名称的cookie的值传入控制器方法参数

2. 属性：

   - value：指定cookie的名称
   - required：是否必须有此cookie

3. 如：

   ```java
   @RequestMapping("/login")
   public String login(@CookieValue(name="JSESSIONID") String cookieValue){
       System.out.println(cookieValue);
       return "login";
   }
   ```

## 3.6 Attribute注解

1. Attribute注解有两种
   - ModelAttribute
   - SessionAttribute

### 3.6.1 ModelAttribute

1. 作用：SpringMVC4.3之后加入的，用于修饰方法和参数

   - 方法：当前方法会在控制器的方法执行之前先执行，无论方法是否有返回值，被注解的方法的返回值作为Model的属性绑定到Model中
   - 参数：将参数绑定到Model中

2. 属性：

   - value：获取数据key，key可以是POJO的属性名称，也可以是map结构的key

3. 应用场景：

   - 当表单提交的数据不是完整的实体类数据时，保证没有提交数据的字段使用数据库对象原来的数据
   - 控制登录权限，当然控制登录权限的方法有很多，例如拦截器、过滤器等

4. 方法上的@ModelAttribute：

   ```java
   @ModelAttribute
   public User showUser(String username){
       User user = new User();
       user.setUsername(username);
       user.setAge(20);	//	设置默认参数
       user.setDate(new Date());
       return user;
   }
   
   @RequestMapping("/login")
   public String login(User user){
       System.out.println(user.getUsername());
       System.out.println(user.getAge());
       System.out.println(user.getDate());
       return "login";
   }
   ```

   ```jsp
   <a href="login?username=jzp"></a>
   ```

5. 参数上的@ModelAttribute：

   ```java
   @ModelAttribute
   public void showUser(String username,Map<String,User> map){
       User user = new User();
       user.setUsername(username);
       user.setAge(20);	//	设置默认参数
       user.setDate(new Date());
       map.add("user",user);
   }
   
   @RequestMapping("/login")
   public String login(@ModelAttribute("user") User user){
       System.out.println(user.getUsername());
       System.out.println(user.getAge());
       System.out.println(user.getDate());
       //使用@ModelAttribute("user")与会自动将参数绑定到Model中，相当于model.addAttribute("user",user)
       return "login";
   }
   ```

### 3.6.2 SessionAttribute

1. 作用：用于多次执行控制器方法之间的参数共享，它只能在类上注解

2. 属性：

   - value：用于指定存入的属性名称
   - type：用于指定存入的数据类型

3. 如：

   ```java
   @Controller
   @SessionAttribute(value={"name"},types={Integer.class})
   public class TestController{
   	@RequestMapping("/login")
   	public String login(){
   		return "login";
   	}
   }
   ```

# 四、数据转换

## 4.1 内置类型转换器

1. Spring MVC框架的 Converter<S，T> 是一个可以将一种数据类型转换成另一种数据类型的接口，这里 S 表示源类型，T 表示目标类型。开发者在实际应用中使用框架内置的类型转换器基本上就够了，但有时需要编写具有特定功能的类型转换器。

2. 标量转换器

   | 名称                           | 作用                                                         |
   | ------------------------------ | ------------------------------------------------------------ |
   | StringToBooleanConverter       | String 到 boolean 类型转换                                   |
   | ObjectToStringConverter        | Object 到 String 转换，调用 toString 方法转换                |
   | StringToNumberConverterFactory | String 到数字转换（例如 Integer、Long 等）                   |
   | NumberToNumberConverterFactory | 数字子类型（基本类型）到数字类型（包装类型）转换             |
   | StringToCharacterConverter     | String 到 Character 转换，取字符串中的第一个字符             |
   | NumberToCharacterConverter     | 数字子类型到 Character 转换                                  |
   | CharacterToNumberFactory       | Character 到数字子类型转换                                   |
   | StringToEnumConverterFactory   | String 到枚举类型转换，通过 Enum.valueOf 将字符串转换为需要的枚举类型 |
   | EnumToStringConverter          | 枚举类型到 String 转换，返回枚举对象的 name 值               |
   | StringToLocaleConverter        | String 到 java.util.Locale 转换                              |
   | PropertiesToStringConverter    | java.util.Properties 到 String 转换，默认通过 ISO-8859-1 解码 |
   | StringToPropertiesConverter    | String 到 java.util.Properties 转换，默认使用 ISO-8859-1 编码 |

3. 集合、数组相关转换器

   | 名称                            | 作用                                                         |
   | ------------------------------- | ------------------------------------------------------------ |
   | ArrayToCollectionConverter      | 任意数组到任意集合（List、Set）转换                          |
   | CollectionToArrayConverter      | 任意集合到任意数组转换                                       |
   | ArrayToArrayConverter           | 任意数组到任意数组转换                                       |
   | CollectionToCollectionConverter | 集合之间的类型转换                                           |
   | MapToMapConverter               | Map之间的类型转换                                            |
   | ArrayToStringConverter          | 任意数组到 String 转换                                       |
   | StringToArrayConverter          | 字符串到数组的转换，默认通过“，”分割，且去除字符串两边的空格（trim） |
   | ArrayToObjectConverter          | 任意数组到 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回数组的第一个元素并进行类型转换 |
   | ObjectToArrayConverter          | Object 到单元素数组转换                                      |
   | CollectionToStringConverter     | 任意集合（List、Set）到 String 转换                          |
   | StringToCollectionConverter     | String 到集合（List、Set）转换，默认通过“，”分割，且去除字符串两边的空格（trim） |
   | CollectionToObjectConverter     | 任意集合到任意 Object 的转换，如果目标类型和源类型兼容，直接返回源对象；否则返回集合的第一个元素并进行类型转换 |
   | ObjectToCollectionConverter     | Object 到单元素集合的类型转换                                |

4. 类型转换是在视图与控制器相互传递数据时发生的。Spring MVC 框架对于基本类型（例如 int、long、float、double、boolean 以及 char 等）已经做好了基本类型转换。

## 4.2 自定义类型转换器

2. 配置自定义类型转换器

   ```xml
   <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
       <property name="converters">
           <set>
               <bean class="utils.StringToDateConverter"/>
           </set>
       </property>
   </bean>
   ```

3. 类型转换器类

   ```java
   package controller;
   
   import org.springframework.core.convert.converter.Converter;
   import java.util.Date;
   
   //将请求中的字符串信息转换为Date类型
   public class StringToDateConverter implements Converter<String,Date> {
       @Override
       public Date convert(String source) {
           //类型转换过程
           return null;
       }
   }
   ```

## 4.3 格式化转换器

1. Spring MVC框架的 Formatter<T> 与 Converter<S，T> 一样，也是一个可以将一种数据类型转换成另一种数据类型的接口。不同的是，Formatter<T> 的源数据类型必须是 String 类型，而 Converter<S，T> 的源数据类型是任意数据类型。

2. 在 Web 应用中由 HTTP 发送的请求数据到控制器中都是以 String 类型获取，因此在 Web 应用中选择 Formatter<T> 比选择 Converter<S，T> 更加合理。

3. Spring MVC 提供了几个内置的格式化转换器，具体如下。

   - NumberFormatter：实现 Number 与 String 之间的解析与格式化。
   - CurrencyFormatter：实现 Number 与 String 之间的解析与格式化（带货币符号）。
   - PercentFormatter：实现 Number 与 String 之间的解析与格式化（带百分数符号）。
   - DateFormatter：实现 Date 与 String 之间的解析与格式化。

4. 自定义格式化转换器

   ```xml
   <!--注册MyFormatter-->
   <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
       <property name="formatters">
           <list>
               <bean class="formatter.MyFormatter"/>
           </list>
       </property>
   </bean>
   ```

   ```java
   package controller;
   import java.text.ParseException;
   import java.text.SimpleDateFormat;
   import java.util.Date;
   import java.util.Locale;
   import org.springframework.format.Formatter;
   public class MyFormatter implements Formatter<Date> {
       SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
       public String print(Date object, Locale arg1) {
           return dateFormat.format(object);
       }
       public Date parse(String source, Locale arg1) throws ParseException {
           return dateFormat.parse(source); // Formatter只能对字符串转换
       }
   }
   ```
   
5. 可以在创建日期的时候自动转换：

   ```xml
   <!-- 注册格式化转换器 -->
   <bean id="conversionService"
    class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
   </bean>
   ```

   ```java
   package com.po;
   
   public class User {
       // 日期格式化（需要在配置文件中配置FormattingConversionServiceFactoryBean)
       @DateTimeFormat(pattern = "yyyy-MM-dd")
       private Date date;
       //getter and setter...
   }
   ```

   

# 五、表单标签库

## 5.1 概述

1. 表单标签库中包含了可以用在 JSP页面中渲染 HTML 元素的标签。在 JSP 页面使用Spring表单标签库时，必须在 JSP 页面开头处声明 taglib 指令，指令代码如下：

   ```jsp
   <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
   ```

2. 在表单标签库中有 form、input、password、hidden、textarea、checkbox、checkboxes、radiobuttton、radiobuttons、select、option、options、errors 等标签，如下：

   | 名称         | 作用                                   |
   | ------------ | -------------------------------------- |
   | form         | 渲染表单元素                           |
   | input        | 渲染 <input type="text"/> 元素         |
   | password     | 渲染 <input type="password"/> 元素     |
   | hidden       | 渲染 <input type="hidden"/> 元素       |
   | textarea     | 渲染 textarea 元素                     |
   | checkbox     | 渲染一个 <input type="checkbox"/> 元素 |
   | checkboxes   | 渲染多个 <input type="checkbox"/> 元素 |
   | radiobutton  | 渲染一个 <input type="radio"/> 元素    |
   | radiobuttons | 渲染多个 <input type="radio"/> 元素    |
   | select       | 渲染一个选择元素                       |
   | option       | 渲染一个选项元素                       |
   | options      | 渲染多个选项元素                       |
   | errors       | 在 span 元素中渲染字段错误             |

## 5.2 表单标签

1. 表单标签的语法格式如下：

   ```jsp
   <form:form modelAttribute="xxx" method="post" action="xxx">
       //其他的子标签
   </form:form>
   ```

2. 表单标签除了具有 HTML 表单元素属性以外，还有以下属性：

   - acceptCharset：定义服务器接受的字符编码列表。
   - commandName：暴露表单对象的模型属性名称，默认为 command。
   - cssClass：定义应用到 form 元素的 CSS 类。
   - cssStyle：定义应用到 form 元素的 CSS 样式。
   - htmlEscape：true 或 false，表示是否进行 HTML 转义。
   - modelAttribute：暴露 form backing object 的模型属性名称，默认为 command。如果controller的Model中没有modelAttribute指定的属性，将会抛出异常

## 5.3 其他标签介绍

1. input标签

   ```jsp
   <form:form modelAttribute="user" method="post" action="user/save">
       <form:input path="userName"/>
   </form:form>
   ```

   - path：将文本框输入值绑定到 form backing object 的一个属性
   - 表示将输入值绑定到 user 对象的 userName 属性。

2. password、hidden和textarea标签的用法与 input 标签完全一致

3. checkbox标签

   ```jsp
   <form:checkbox path="friends" value="张三"/>张三
   <form:checkbox path="friends" value="李四"/>李四
   <form:checkbox path="friends" value="王五"/>王五
   <form:checkbox path="friends" value="赵六"/>赵六
   ```

   - 复选框的值绑定到一个字符串数组属性 friends（String[] friends）

4. checkboxes标签

   ```jsp
   <form:checkboxes items="${hobbys}" path="hobby"/>
   ```

   - items：用于生成 input 元素的 Collection、Map 或 Array。
   - itemLabel：items 属性中指定的集合对象的属性，为每个 input 元素提供 label。
   - itemValue：items 属性中指定的集合对象的属性，为每个 input 元素提供 value。
   - checkboxes 标签渲染多个复选框，是一个选项组，等价于多个 path 相同的 checkbox 标签
   - 将 model 属性 hobbys 的内容（集合元素）渲染为复选框。在 itemLabel 和 itemValue 省略的情况下，如果集合是数组，复选框的 label 和 value 相同；如果是 Map 集合，复选框的 label 是 Map 的值（value），复选框的 value 是 Map 的关键字（key）。

5. radiobutton标签：类似于checkbox标签

6. radiobuttons标签：类似于checkboxes标签

7. select标签

   ```jsp
   <form:select path="xxx" items="xxx">
       <option value="xxx">xxx</option>
   </form:select>
   ```

8. option标签

   ```jsp
   <form:select path="xxx">
       <form:options items="xxx"/>
   </form:select>
   ```

9. errors标签

   ```jsp
   <form:errors path="xxx"/>
   ```

# 六、拦截器

## 6.1 概述

1. Spring MVC 的拦截器（Interceptor）与 Java Servlet的过滤器（Filter）类似，它主要用于拦截用户的请求并做相应的处理，通常应用在权限验证、记录请求信息的日志、判断用户是否登录等功能上。
2. 定义一个拦截器可以通过两种方式：
   - 实现 HandlerInterceptor 接口或继承 HandlerInterceptor 接口的实现类
   - 实现 WebRequestInterceptor 接口或继承 WebRequestInterceptor 接口的实现类，功能没有HandlerInterceptor接口强大，一般不用

## 6.2 拦截器的实现

1. 定义一个类，实现HandlerInterceptor接口

   ```java
   package interceptor;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import org.springframework.web.servlet.HandlerInterceptor;
   import org.springframework.web.servlet.ModelAndView;
   public class TestInterceptor implements HandlerInterceptor {
       @Override
       public void afterCompletion(HttpServletRequest request,
               HttpServletResponse response, Object handler, Exception ex)
               throws Exception {
           System.out.println("afterCompletion方法在控制器的处理请求方法执行完成后执行，即视图渲染结束之后执行");
       }
       @Override
       public void postHandle(HttpServletRequest request,
               HttpServletResponse response, Object handler,
               ModelAndView modelAndView) throws Exception {
           System.out.println("postHandle方法在控制器的处理请求方法调用之后，解析视图之前执行");
       }
       @Override
       public boolean preHandle(HttpServletRequest request,
               HttpServletResponse response, Object handler) throws Exception {
           System.out.println("preHandle方法在控制器的处理请求方法调用之后，解析视图之前执行");
           return false;
       }
   }
   ```

   - preHandle 方法：该方法在控制器的处理请求方法前执行，其返回值表示是否中断后续操作，返回 true 表示继续向下执行，返回 false 表示中断后续操作。
   - postHandle 方法：该方法在控制器的处理请求方法调用之后、解析视图之前执行，可以通过此方法对请求域中的模型和视图做进一步的修改。
   - afterCompletion 方法：该方法在控制器的处理请求方法执行完成后执行，即视图渲染结束后执行，可以通过此方法实现一些资源清理、记录日志信息等工作。

2. 配置拦截器（springmvc-servlet.xml）

   ```xml
   <!-- 配置拦截器 -->
   <mvc:interceptors>
       <!-- 配置一个全局拦截器，拦截所有请求 -->
       <bean class="interceptor.TestInterceptor" /> 
       <mvc:interceptor>
           <!-- 配置拦截器作用的路径 -->
           <mvc:mapping path="/**" />
           <!-- 配置不需要拦截作用的路径 -->
           <mvc:exclude-mapping path="/gotoTest" />
           <!-- 定义<mvc:interceptor>元素中，表示匹配指定路径的请求才进行拦截 -->
           <bean class="interceptor.Interceptor1" />
       </mvc:interceptor>
   </mvc:interceptors>
   ```

   - <mvc：interceptors> ：用于配置一组拦截器
   - <bean>：mvc：interceptors的直接子元素是定义一个全局拦截器，即拦截所有的请求；interceptor的直接子元素是拦截指定路径的请求
   - <mvc：interceptor>：定义指定路径的拦截器
   - <mvc：mapping>：配置拦截器作用的路径，该路径在其属性 path 中定义。
   - <mvc：exclude-mapping>：请求路径中包含不需要拦截的内容
   - /**：表示拦截所有路径
   - /gotoTest：表示不拦截所有以“/gotoTest”结尾的路径
   - <mvc：interceptor> 元素的子元素必须按照 <mvc：mapping.../>、<mvc：exclude-mapping.../>、<bean.../> 的顺序配置。

3. 配置举例

   ```xml
   <!-- 配置拦截器 -->
       <mvc:interceptors>
           <!-- 配置一个全局拦截器，拦截所有请求 -->
           <bean class="interceptor.TestInterceptor" />
       </mvc:interceptors>
   ```

   ```xml
   <mvc:interceptors>
       <mvc:interceptor>
           <!-- 配置拦截器作用的路径 -->
           <mvc:mapping path="/**"/>
           <!--定义在<mvc:interceptor>元素中，表示匹配指定路径的请求才进行拦截-->
           <bean class="interceptor.Interceptor1"/>
       </mvc:interceptor>
       <mvc:interceptor>
           <!-- 配置拦截器作用的路径 -->
           <mvc:mapping path="/gotoTest"/>
           <!--定义在<mvc:interceptor>元素中，表示匹配指定路径的请求才进行拦截-->
           <bean class="interceptor.Interceptor2"/>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

## 6.3 执行流程

1. 一个拦截器的执行流程如下：
   - 首先，执行拦截器类中的 preHandle 方法
   - 然后，执行控制器中处理请求的方法
   - 然后，执行拦截器类中 postHandle 方法
   - 然后，渲染视图
   - 最后，执行控制器中的 afterCompletion 方法
2. 对于拦截器中的preHandle方法，如果方法返回 true，程序将继续往下执行，否则中断执行。
3. 如果有多个拦截器，它们的 preHandle 方法将按照配置文件中拦截器的配置顺序执行，而它们的 postHandle 方法和 afterCompletion 方法则按照配置顺序的反序执行。



# 七、验证器

## 7.1 概述

1. 验证器是Spring MVC验证用户输入的方法，用户的输入一般是随意的，为了保证数据的合法性，数据验证是所有 Web 应用必须处理的问题。
2. 在Spring MVC 中有两种方法可以验证输入数据：
   - 利用Spring自带的验证框架
   - 利用JSR303实现
3. 数据验证分为两种：
   - 客户端验证：客户端验证主要是过滤正常用户的误操作，通过 JavaScript 代码完成
   - 服务器端验证：服务器端验证是整个应用阻止非法数据的最后防线，通过在应用中编程实现。
4. 客户端验证可以过滤用户的误操作，是第一道防线，一般使用 JavaScript 代码实现。在大多数情况下，使用 JavaScript 进行客户端验证的步骤如下：
   - 编写验证函数。
   - 在提交表单的事件中调用验证函数。
   - 根据验证函数来判断是否进行表单提交。
5. 服务器端验证对于系统的安全性、完整性、健壮性起到了至关重要的作用。Spring MVC 框架中先进行数据类型转换（Converter或Formatter），再进行服务器端验证

## 7.2 SpringMVC验证器

1. 定义一个SpringMVC验证器需要实现Validator接口，然后可以使用工具类ValidationUtils进行验证

### 7.2.1 Validator接口

1. 例如：

   ```java
   package com.validator;
   
   import org.springframework.validation.Errors;
   import org.springframework.validation.ValidationUtils;
   import org.springframework.validation.Validator;
   
   public class TestValidator implements Validator {
       @Override
       public boolean supports(Class<?> clazz) {
           // 要验证的model,返回值为false则不验证
           return false;
       }
   
       @Override
       public void validate(Object target, Errors errors) {
       }
   }
   ```

   - boolean supports(Class<?> klass)：当 supports 方法返回 true 时，验证器可以处理指定的 Class。
   - void validate(Object object,Errors errors)：验证目标对象 object，并将验证错误消息存入 Errors 对象。

2. 往 Errors 对象存入错误消息的方法：

   - void reject(String errorCode)
   - void reject(String errorCode,String defaultMessage)
   - void rejectValue(String filed,String errorCode)
   - void rejectValue(String filed,String errorCode,String defaultMessage)

   例如：

   ```java
   if(sex == ""){
       errors.rejectValue("sex","性别不能为空");  // gprice.invalid为错误代码
   }
   ```

### 7.2.2 ValidationUtils类

1. ValidationUtils是一个工具类，提供了一些静态方法：

   - static void rejectIfEmpty()：相当于`s==""?`
   - static void rejectIfEmptyOrWhitespace()：相当于`s==null||s==""?`

2. 例如

   ```java
   if(sex.trim() == ""){
       errors.rejectValue("sex","性别不能为空");  // gprice.invalid为错误代码
   }
   ```

   可以改成：

   ```java
   ValidationUtils.rejectIfEmpty(errors,"sex","性别不能为空");
   ```

### 7.2.3 实例

1. POJO

   ```java
   package com.po;
   
   public class User {
       private String username;
       private String password;
       //getter and setter...
   }
   ```

2. jsp

   ```jsp
   <form:form modelAttribute="user" action="/logout" method="post">
       <fieldset>
           <legend> 添加一件商品 </legend>
           <P>
               <label>用户名:</label>
               <form:input path="username" />
               </p>
           <p>
               <label>密码:</label>
               <form:input path="password" />
           </p>
           <p id="buttons">
               <input id="reset" type="reset">
               <input id="submit" type="submit" value="注册">
           </p>
       </fieldset>
       <!-- 取出所有验证错误 -->
       <form:errors path="*" />
   </form:form>
   ```

3. 验证器类

   ```java
   package com.validator;
   
   import com.po.User;
   import org.springframework.stereotype.Component;
   import org.springframework.validation.Errors;
   import org.springframework.validation.ValidationUtils;
   import org.springframework.validation.Validator;
   
   @Component
   public class LogoutValidator implements Validator {
       @Override
       public boolean supports(Class<?> clazz) {
           //clazz是否可以转变成User，如果不行，则返回false，不进行验证
           return User.class.isAssignableFrom(clazz);
       }
   
       @Override
       public void validate(Object target, Errors errors) {
           User user = (User) target;
           //验证用户名是否为空
           ValidationUtils.rejectIfEmpty(errors, "username", "user.username.required");
           //验证密码长度是否小于10
           if (user.getPassword().length() < 10) {
               errors.rejectValue("password","user.password.required");
           }
       }
   }
   ```

4. 创建message.properties文件，用于保存错误信息，之后文件需要保存为unicode?

   ```properties
   user.username.required=用户名不能为空
   user.password.required=密码长度不能小于10
   ```

5. 控制器类

   ```java
   package com.controller;
   
   import com.dao.UserDao;
   import com.po.User;
   import com.validator.LogoutValidator;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.validation.BindingResult;
   import org.springframework.web.bind.annotation.ModelAttribute;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   import javax.annotation.Resource;
   
   @Controller
   public class LogoutController {
   
       @Autowired
       private UserDao userDao;
   
       @Resource
       private LogoutValidator logoutValidator;
   
       @RequestMapping("/logout")
       public String logout(@ModelAttribute User user, BindingResult result) {
           this.logoutValidator.validate(user, result); // 添加验证
           if (result.hasErrors()) {
               return "logout";
           }
           userDao.save(user);
           return "success";
       }
   
   }
   ```

6. Springmvc配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
       <!-- 使用扫描机制扫描包 -->
       <context:component-scan base-package="com.controller" />
       <context:component-scan base-package="com.validator" />
       <!-- 完成视图的对应 -->
       <!-- 对转向页面的路径解析。prefix：前缀， suffix：后缀 -->
       <bean
           class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp" />
       </bean>
   
       <mvc:annotation-driven />
   
       <!-- 配置消息属性文件 -->
       <bean id="messageSource"
    class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
           <property name="basename" value="message" />
       </bean>
   </beans>
   ```

## 7.3 JSR303验证

1. 对于 JSR 303 验证，目前有两个实现：
   - Hibernate Validator：官方推荐
   - Apache BVal
2. Hibernate Validator和 Hibernate 无关，只是使用它进行数据验证。

### 7.3.1 Hibernate Validator

1. 引入maven

   ```xml
   <dependency>
       <groupId>org.hibernate</groupId>
       <artifactId>hibernate-validator</artifactId>
       <version>5.4.1.Final</version>
   </dependency>
   ```

2. 和SpringMVC验证器不一样的是，Hibernate Validator需要在配置文件中配置属性文件，并将属性文件与 Hibernate Validator 关联。

   ```xml
   <!-- 配置消息属性文件 -->
   <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
       <!-- 资源文件名 -->
       <property name="basenames">
           <list>
               <value>messege</value>
           </list>
       </property>
       <!-- 资源文件编码格式 -->
       <property name="fileEncodings" value="utf-8" />
       <!-- 对资源文件内容缓存的时间，单位为秒 -->
       <property name="cacheSeconds" value="120" />
   </bean>
   <!-- 注册校验器 -->
   <bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
       <!-- hibernate 校验器 -->
       <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
       <!-- 指定校验使用的资源文件，在文件中配置校验错误信息，如果不指定则默认使用 classpath下的 ValidationMessages.properties -->
       <property name="validationMessageSource" ref="messageSource" />
   </bean>
   <!--开启 Spring的 Valid 功能 -->
   <mvc:annotation-driven conversion-service="conversionService" validator="validator" />
   ```

3. JSR 303 不需要编写验证器，但需要利用它的标注类型在领域模型的属性上嵌入约束。

4. 因为不需要编写验证器，即不需要实现Validator，所以在控制器中使用时也不需要实例化验证器，只要在配置文件中配置了HibernateValidator验证器，就会自动去验证经类型转化器转换后的实体类属性数据。在控制器中就可以通过BindingResult获取验证错误了。

### 7.3.2 标注类型

1. 空检查

   - @Null：验证对象是否为 null。
   - @NotNull：验证对象是否不为 null，无法检查长度为 0 的字符串。
   - @NotBlank：检查约束字符串是不是 null，以及被 trim 后的长度是否大于 0，只针对字符串，且会去掉前后空格。
   - @NotEmpty：检查约束元素是否为 null 或者是 empty。

   ```java
   @NotBlank(message="{user.username.required}") //user.username.required为属性文件的错误代码
   private String username;
   ```

2. boolean检查

   - @AssertTrue：验证 boolean 属性是否为 true。
   - @AssertFalse：验证 boolean 属性是否为 false。

   ```java
   @AssertTrue
   private boolean isLogin;
   ```

3. 长度检查

   - @Size（min=，max=）：验证对象（Array、Collection、Map、String）长度是否在给定的范围之内。
   - @Length（min=，max=）：验证字符串长度是否在给定的范围之内。

   ```java
   @Length(min=10,max=100)
   private String password;
   ```

4. 日期检查

   - @Past：验证 Date 和 Callendar 对象是否在当前时间之前。
   - @Future：验证 Date 和 Calendar 对象是否在当前时间之后。
   - @Pattern：验证 String 对象是否符合正则表达式的规则。

   ```java
   @Past(message="{date.invalid}")
   private Date date;
   ```

5. 数值检查

   - @Min：验证 Number 和 String 对象是否大于指定的值
   - @Max：验证 Number 和 String 对象是否小于指定的值
   - @DecimalMax：被标注的值必须不大于约束中指定的最大值，这个约束的参数是一个通过 BigDecimal 定义的最大值的字符串表示，小数存在精度
   - @DecimalMin：被标注的值必须不小于约束中指定的最小值，这个约束的参数是一个通过 BigDecimal 定义的最小值的字符串表示，小数存在精度
   - @Digits：验证 Number 和 String 的构成是否合法
   - @Digits（integer=，fraction=）：验证字符串是否符合指定格式的数字，integer 指定整数精度，fraction 指定小数精度
   - @Range（min=，max=）：检查数字是否介于 min 和 max 之间
   - @Valid：对关联对象进行校验，如果关联对象是个集合或者数组，那么对其中的元素进行校验，如果是一个 map，则对其中的值部分进行校验
   - @CreditCardNumber：信用卡验证
   - @Email：验证是否为邮件地址，如果为 null，不进行验证，通过验证

   ```java
   @Range(min=0,max=120,message="{{user.age.required}}")
   private int age;
   ```

### 7.4.3 实例

1. POJO

   ```java
   package com.po;
   
   public class User {
       @NotBlank(message="{user.username.required}")
       private String username;
       @Length(min=6,max=12)
       private String password;
       //getter and setter...
}
   ```
   
2. Controller

   ```java
   @RequestMapping("/register")
   public String register(@ModelAttribute User user, Errors errors) {
       if (errors.hasErrors()) {
           System.out.println(errors.getAllErrors());
       } else {
       }
       return "info";
   }
   ```

3. 配置，往上翻



# 八、国际化

## 8.1  概述

1. 程序国际化已成为 Web 应用的基本要求。随着网络的发展，大部分 Web 站点面对的已经不再是本地或者本国的浏览者，而是来自全世界各国、各地区的浏览者，因此国际化成为了 Web 应用不可或缺的一部分。
2. Java国际化的思想是将程序中的信息放在资源文件中，程序根据支持的国家及语言环境读取相应的资源文件。资源文件是 key-value 对，每个资源文件中的 key 是不变的，但 value 随不同国家/语言变化。
3. Spring MVC的国际化是建立在 Java 国际化的基础之上的，Spring MVC 框架的底层国际化与 Java 国际化是一致的，作为一个良好的 MVC 框架，Spring MVC 将 Java 国际化的功能进行了封装和简化，开发者使用起来会更加简单、快捷。
4. 国际化和本地化应用程序时需要具备以下两个条件：
   - 将文本信息放到资源属性文件中。
   - 选择和读取正确位置的资源属性文件。

## 8.2 Java国际化

1. Java 程序的国际化主要通过两个类来完成：

   - java.util.Locale：用于提供本地信息，通常称它为语言环境。不同的语言、不同的国家和地区采用不同的 Locale 对象来表示。

   - java.util.ResourceBundle：该类称为资源包，包含了特定于语言环境的资源对象。当程序需要一个特定于语言环境的资源时（例如字符串资源），程序可以从适合当前用户语言环境的资源包中加载它。采用这种方式可以编写独立于用户语言环境的程序代码，而与特定语言环境相关的信息则通过资源包来提供。

2. 为了实现 Java 程序的国际化，必须事先提供程序所需要的资源文件。资源文件的内容由很多 key-value 对组成，其中 key 是程序使用的部分，而 value 是程序界面的显示。资源文件的命名可以有如下 3 种形式：

   - baseName.properties
   - baseName_language.properties
   - baseName_language_country.properties

3. baseName 是资源文件的基本名称，由用户自由定义，但是 language 和 country 必须为 Java 所支持的语言和国家/地区代码。例如：

   - 中国大陆：baseName_zh_CN.properties
   - 美国：baseName_en_US.properties

4. Java 中的资源文件只支持 ISO-8859-1 编码格式字符，直接编写中文会出现乱码，可以使用 Java 命令 native2ascii.exe 解决资源文件的中文乱码问题

5. 在java.util.Locale类里面，有所有支持的语言说明

6. java读取不同语言的properties文件

   - 假如有以下properties文件：
     - messageResource_zh_CN.properties：`word=你好`
     - messageResource_ en_US.properties：`word=hello`
     - messageResource_zh.properties
     - messageResource.properties
   - java代码：

   ```java
   // 取得系统默认的国家语言环境
   Locale lc = Locale.getDefault();
   // 根据国家语言环境加载资源文件
   ResourceBundle rb = ResourceBundle.getBundle("messageResource", lc);
   // 打印出从资源文件中取得的信息
   System.out.println(rb.getString("hello"));
   ```

7. bundle文件搜索顺序

   - 搜索国家/语言都匹配的资源文件
   - 如果没有，就搜索语言匹配文件
   - 还没有，就搜索baseName 匹配的文件
   - 以上3中如果都不存在，就会抛出异常

8. 带占位符的国际化信息：

   ```java
   import java.text.MessageFormat;
   import java.util.Locale;
   import java.util.ResourceBundle;
   
   // 取得系统默认的国家语言环境
   Locale lc = Locale.getDefault();
   // 根据国家语言环境加载资源文件
   ResourceBundle rb = ResourceBundle.getBundle("messageResource", lc);
   // 从资源文件中取得的信息
   String msg = rb.getString("word");
   // 替换消息文本中的占位符，消息文本中的数字占位符将按照参数的顺序
   // （从第二个参数开始）被替换，即“我”替换{0}、“5”替换{1}
   String msgFor = MessageFormat.format(msg, "中国", "啊");
   System.out.println(msgFor);	//中国你好啊
   ```

   ```properties
   word={0}你好{1}
   ```

## 8.3 SpringMVC国际化

1. SpringMVC实现一个国际化应用主要有三步：

   - 配置资源属性文件
   - 配置区域语言选择
   - 使用message标签将数据显示在jsp中

2. 配置资源属性文件（springmvc-servlet.xml）

   - 对于单个属性文件

   ```xml
   <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
       <property name="basenames" value="messages"/>
   </bean>
   ```

   - 对于多个属性文件

   ```xml
   <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
       <property name="basenames">
           <list>
               <!-- 可以直接写绝对路径 -->
               <value>/WEB-INF/resource/messages</value>
               <value>/WEB-INF/resource/labels</value>
           </list>
       </property>
   </bean>
   ```

3. 区域语言的选择需要使用Springmvc的区域语言选择器（LocaleResolver），该接口有3个常见实现：

   - AcceptHeaderLocaleResolver：根据浏览器 Http Header 中的 accept-language 域设定（accept-language 域中一般包含了当前操作系统的语言设定，可通过 HttpServletRequest.getLocale 方法获得此域的内容）。改变 Locale 是不支持的，即不能调用 LocaleResolver 接口的 setLocale（HttpServletRequest request, HttpServletResponse response, Locale locale）方法设置 Locale。
   - SessionLocaleResolver：根据用户本次会话过程中的语言设定决定语言区域（例如用户进入首页时选择语言种类，则此次会话周期内统一使用该语言设定）。（一般使用的是这个）
   - CookieLocaleResolver：根据 Cookie 判定用户的语言设定（Cookie 中保存着用户前一次的语言设定参数）。

4. 配置区域语言选择器（springmvc-servlet.xml）

   - 使用SessionLocaleResolver

   ```xml
   <bean id="localeResolver" class="org.springframework.web.servlet.il8n.SessionLocaleResolver">
       <property name="defaultLocale" value="zh_CN"></property>
   </bean>
   ```

   - SessionLocaleResolver 或CookieLocaleResolver，必须配置 LocaleChangeInterceptor 拦截器

   ```xml
   <mvc:interceptors>
       <bean class="org.springframework.web.servlet.il8n.LocaleChangeInterceptor"/>
   </mvc:interceptors>
   ```

5. 使用message标签将数据显示在jsp中

   ```jsp
   <%@taglib prefix="spring" uri="http://www.springframework.org/tags"%>
   <spring:message code="word" />中国你好啊
   ```

   - code：获得国际化消息的 key。
   - arguments：代表该标签的参数。如果替换消息中的占位符，示例代码为“<spring：message code="word" arguments="中国,啊" />”，third 对应的消息有两个占位符 {0} 和 {1}。
   - argumentSeparator：用来分隔该标签参数的字符，默认为逗号。
   - text：code 属性不存在，或指定的 key 无法获取消息时所显示的默认文本信息。

## 8.4 自定义语言切换

1. 控制器

   ```java
   package controller;
   import java.util.Locale;
   import org.springframework.web.bind.annotation.RequestMapping;
   @Controller
   public class I18NTestController {
       @RequestMapping("/i18nTest")
       /**
        * locale接收请求参数locale值，并存储到session中
        */
       public String i18nTest(Locale locale) {
           return "index";
       }
   }
   ```

2. jsp

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
       pageEncoding="UTF-8"%>
   <%@taglib prefix="spring" uri="http://www.springframework.org/tags"%>
   <a href="/i18nTest?locale=zh_CN">中文</a>
   <a href="/i18nTest?locale=en_US">英文</a>
   ```


# 九、异常处理

## 9.1 概述

1. Springmvc支持统一异常处理，有以下3中实现方式：

   - 使用 Spring MVC 提供的简单异常处理器 SimpleMappingExceptionResolver。
   - 实现 Spring 的异常处理接口 HandlerExceptionResolver 自定义自己的异常处理器。
   - 使用 @ExceptionHandler 注解实现异常处理

2. 对于运行时异常，由于代码不强制捕获，往往被忽略，如果运行期产生了 Unchecked Exception，而代码中又没有进行相应的捕获和处理，则可能不得不面对 404、500 等服务器内部错误提示页面，所以在 web.xml 文件中添加了全局异常 404 处理。具体代码如下：

   ```xml
   <error-page>
       <error-code>404</error-code>
       <location>/WEB-INF/jsp/404.jsp</location>
   </error-page>
   ```

## 9.2 简单映射异常解析器

1. SimpleMappingExceptionResolver的配置（springmvc-servlet.xml）：

   ```xml
   <!--SimpleMappingExceptionResolver（异常类与 View 的对应关系） -->
   <bean
         class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
       <!-- 定义默认的异常处理页面，当该异常类型注册时使用 -->
       <property name="defaultErrorView" value="error"></property>
       <!-- 定义异常处理页面用来获取异常信息的变量名，默认名为exception -->
       <property name="exceptionAttribute" value="ex"></property>
       <!-- 定义需要特殊处理的异常，用类名或完全路径名作为key，异常页名作为值 -->
       <property name="exceptionMappings">
           <props>
               <prop key="exception.MyException">my-error</prop>
               <prop key="java.sql.SQLException">sql-error</prop>
               <!-- 在这里还可以继续扩展对不同异常类型的处理 -->
           </props>
       </property>
   </bean>
   ```

2. 在以上的配置中，jsp页面要获取相应异常，可以通过定义的变量ex来获取，对于异常处理的页面，有如下：

   - 对于未知异常（并没有定义的异常），会渲染error.jsp页面
   - 对于自定义的异常，会渲染my-error.jsp页面，一般行配置会放在所有异常配置的前面
   - 对于SQL异常，会渲染sql-error.jsp页面

## 9.3 处理异常解析器

1. 自定义自己的异常处理器需要实现HandlerExceptionResolver接口：

   ```java
   package exception;
   
   import java.sql.SQLException;
   import java.util.HashMap;
   import java.util.Map;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import org.springframework.web.servlet.HandlerExceptionResolver;
   import org.springframework.web.servlet.ModelAndView;
   public class MyExceptionHandler implements HandlerExceptionResolver {
       @Override
       public ModelAndView resolveException(HttpServletRequest arg0,
               HttpServletResponse arg1, Object arg2, Exception arg3) {
           Map<String, Object> model = new HashMap<String, Object>();
           model.put("ex", arg3);
           // 根据不同错误转向不同页面（统一处理），即异常与View的对应关系
           if (arg3 instanceof MyException) {
               return new ModelAndView("my-error", model);
           } else if (arg3 instanceof SQLException) {
               return new ModelAndView("sql-error", model);
           } else {
               return new ModelAndView("error", model);
           }
       }
   }
   ```

2. 注入自定义异常（springmvc-servlet.xml）：

   ```xml
   <!--托管MyExceptionHandler-->
   <bean class="exception.MyExceptionHandler"/>
   ```

## 9.4 注解实现异常

1. 使用@ExceptionHandler自定义异常时可以不用在springmvc配置文件中配置，但需要让所有需要异常处理的 Controller都继承自定义异常处理类

2. 自定义一个异常处理类（不需要实现HandlerExceptionResolver接口）：

   ```java
   package controller;
   import java.sql.SQLException;
   import javax.servlet.http.HttpServletRequest;
   import org.springframework.web.bind.annotation.ExceptionHandler;
   import exception.MyException;
   public class BaseController {
       /** 基于@ExceptionHandler异常处理 */
       @ExceptionHandler
       public String exception(HttpServletRequest request, Exception ex) {
           request.setAttribute("ex", ex);
           // 根据不同错误转向不同页面，即异常与view的对应关系
           if (ex instanceof SQLException) {
               return "sql-error";
           } else if (ex instanceof MyException) {
               return "my-error";
           } else {
               return "error";
           }
       }
   }
   ```

3. 将所有需要异常处理的 Controller 都继承 BaseController 类，如下：

   ```java
   @Controller
   public class TestExceptionController extends BaseController{
       ...
   }
   ```

   

# 十、文件传输

## 10.1 概述

1. Spring MVC 框架的文件上传是基于 commons-fileupload 组件的文件上传，只不过 Spring MVC 框架在原有文件上传组件上做了进一步封装，简化了文件上传的代码实现，取消了不同上传组件上的编程差异。

2. Commons 是 Apache 开放源代码组织中的一个 Java子项目，该项目包括文件上传、命令行处理、数据库连接池、XML 配置文件处理等模块，而commons-fileupload 组件是Commons项目中用来处理基于表单的文件上传的子项目

3. ommons-fileupload依赖和commons-io依赖

   ```xml
   <dependency>  
       <groupId>commons-fileupload</groupId>  
       <artifactId>commons-fileupload</artifactId>  
       <version>1.3.1</version>  
   </dependency>  
   <dependency>  
       <groupId>commons-io</groupId>  
       <artifactId>commons-io</artifactId>  
       <version>2.4</version>  
   </dependency>  
   ```

## 10.2 文件上传

1. 对于表单文件的上传，需要在前端使用file类型的input标签，并设置表单的enctype属性，如下：

   ```jsp
   <form method="post" action="upload" enctype="multipart/form-data">
       <input type="file" name="myfile"/>
   </form>
   ```

   - enctype的属性值有一下三个值：
     - application/x-www-form-urlencoded：这是默认的编码方式，它只处理表单域里的 value 属性值。
     - multipart/form-data：该编码方式以二进制流的方式来处理表单数据，并将文件域指定文件的内容封装到请求参数里。
     - text/plain：该编码方式只有当表单的 action 属性为“mailto：”URL 的形式时才使用，主要适用于直接通过表单发送邮件的方式。

2. 在 Spring MVC 框架中上传文件时将文件相关信息及操作封装到 MultipartFile 对象中，因此开发者只需要使用 MultipartFile 类型声明模型类的一个属性即可对被上传文件进行操作。该接口具有如下方法：

   - byte[] getBytes()：以字节数组的形式返回文件的内容
   - String getContentType()：返回文件的内容类型
   - InputStream getInputStream()：返回一个InputStream，从中读取文件的内容
   - String getName()：返回请求参数的名称
   - String getOriginalFillename()：返回客户端提交的原始文件名称
   - long getSize()：返回文件的大小，单位为字节
   - boolean isEmpty()：判断被上传文件是否为空
   - void transferTo(File destination)：将上传文件保存到目标目录下

3. 在上传文件时需要在配置文件中使用 Spring 的 `org.springframework.web.multipart.commons.CommonsMultipartResolver` 类配置 MultipartResolver 用于文件上传。

4. 配置spring的CommonsMultipartResolver（springmvc-servlet.xml）：

   ```xml
   <!-- 配置MultipartResolver，用于上传文件，使用spring的CommonsMultipartResolver -->
   <bean id="multipartResolver"
         class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
       <property name="maxUploadSize" value="5000000" />
       <property name="defaultEncoding" value="UTF-8" />
   </bean>
   ```

5. 文件上传的POJO：

   ```java
   import org.springframework.web.multipart.MultipartFile;
   public class FileDomain {
       private String description;
       private MultipartFile myfile;
       //getter and setter
   }
   ```

6. 文件上传的Controller：

   ```java
   @RequestMapping("/onefile")
   public String oneFileUpload(@ModelAttribute FileDomain fileDomain,
                               HttpServletRequest request) {
       /*
        * 文件上传到服务器的位置“/uploadfiles”,该位置是指
        * workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps
        * 发布后使用
        */
       String realpath = request.getServletContext().getRealPath("uploadfiles");
       String fileName = fileDomain.getMyfile().getOriginalFilename();
       File targetFile = new File(realpath, fileName);
       if (!targetFile.exists()) {
           targetFile.mkdirs();
       }
       // 上传
       try {
           fileDomain.getMyfile().transferTo(targetFile);
       } catch (Exception e) {
           e.printStackTrace();
       }
       return "showOne";
   }
   ```

## 10.3 文件下载

1. 实现文件下载有以下两种方法：

   - 通过超链接实现下载：简单，但暴露了下载文件的真实位置，并且只能下载存放在 Web 应用程序所在的目录下的文件。
   - 利用程序编码实现下载：可以增加安全访问控制，还可以从任意位置提供下载的数据，可以将文件存放到 Web 应用程序以外的目录中，也可以将文件保存到数据库中。

2. 实现文件下载需要设置以下报头

   ```java
   response.setHeader("Content-Type", "application/x-msdownload");
   response.setHeader("Content-Disposition", "attachment;filename="+filename);
   ```

   - application/x-msdownload：Web 服务器需要告诉浏览器其所输出内容的类型不是普通文本文件或 HTML 文件，而是一个要保存到本地的下载文件
   - Content-Disposition：Web 服务器希望浏览器不直接处理相应的实体内容，而是由用户选择将相应的实体内容保存到一个文件中，可以指定接收程序处理数据内容的方式
   - 在 HTTP 应用中只有 attachment 是标准方式，attachment 表示要求用户干预。在 attachment 后面还可以指定 filename 参数，该参数是服务器建议浏览器将实体内容保存到文件中的文件名称。



# 十一、SSM环境搭建

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jzp</groupId>
    <artifactId>Springmvcgather</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>Springmvcgather Maven Webapp</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <spring.version>5.0.2.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
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
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
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
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.4.1.Final</version>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
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
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.5</version>
        </dependency>
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>Springmvcgather</finalName>
        <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
            <plugins>
                <plugin>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>3.1.0</version>
                </plugin>
                <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
                <plugin>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>3.0.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                </plugin>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.22.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>3.2.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-install-plugin</artifactId>
                    <version>2.5.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.2</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
</project>
```

## web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <display-name>springMVC</display-name>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <servlet>
        <servlet-name>ssm</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置springMVC需要加载的配置文件
			Mybatis：spring-dao.xml,
			spring：spring-service.xml
			springmvc：spring-web.xml
		 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-*.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>ssm</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/</url-pattern>
    </filter-mapping>
</web-app>
```

## Spring配置文件

（spring-service.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 扫描service包下所有使用注解的类型 -->。
    <!-- dao包在mybatis配置文件中已经扫描过，这里不再需要扫描 -->
    <context:component-scan base-package="com.service" />
    <!-- 添加事务支持 -->
    <bean id="txManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <!-- 开启事务注解 -->
    <tx:annotation-driven transaction-manager="txManager" />
</beans>
```



## Spring数据库配置文件

（spring-dao.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 加载properties文件 -->
    <context:property-placeholder location="classpath:c3p0-db.properties" />
    <!--配置数据源 -->
    <!-- 配置数据源，读取properties文件信息 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}" />
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />
        <property name="user" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
        <property name="initialPoolSize" value="${jdbc.initialPoolSize}"/> <!-- 默认5-->
        <property name="maxPoolSize" value="${jdbc.maxPoolSize}"/> <!-- 默认10-->
        <property name="checkoutTimeout" value="${jdbc.checkoutTimeout}"/> <!--默认3000-->
    </bean>

    <!-- 配置mybitas工厂，同时指定数据源，并与MyBatis完美整合 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- configLocation的属性值为Mybatis的核心配置文件 -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
        <!-- 扫描pojo包 使用别名 -->
		<property name="typeAliasesPackage" value="com.pojo" />
        <!-- 扫描sql配置文件:mapper需要的xml文件 -->
		<property name="mapperLocations" value="classpath:mapper/*.xml" />
    </bean>

    <!-- Mapper代理开发，使用Spring自动扫描Mybatis的接口并装配 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- mybatis-spring组件的扫描器 -->
        <property name="basePackage" value="com.dao" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>
</beans>
```

## Springmvc配置文件

（spring-mvc.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/mvc
                           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 简化注解 -->
    <mvc:annotation-driven />
    
    <!-- 静态文件扫描 -->
    <mvc:resources location="/static/" mapping="/static/**" />
    
    <context:component-scan base-package="com.controller"/>
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
</beans>
```

## c3p0-db.properties

```properties
jdbc.driverClass = com.mysql.jdbc.Driver
jdbc.jdbcUrl = jdbc:mysql://localhost:3306/test
jdbc.user = root
jdbc.password = 1415
jdbc.initialPoolSize = 5
jdbc.maxPoolSize = 10
jdbc.checkoutTimeout = 3000
```



## MyBatis配置文件

（mybatis-config.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 映射器告诉MyBatis到哪里去找映射文件，如果在sping-dao文件里面配置了，可以自动寻找 -->
    <!-- 
    <mappers>
        <mapper resource="com/dao/UserMapper.xml" />
    </mappers>
	-->
	<settings>
        <!-- 配置主键自增(主键默认自增) -->
    	<!-- <setting name="useGeneratedKeys" value="false"/> -- >
    </settings>
</configuration> 
```

## MyBatis映射文件

（UserDao.xml）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.UserDao">
    <!-- 配置查询所有 -->
    <!-- 需要配置别名com.pojo.User > user，可以在mybatis配置文件里配置，也可以在spring-dao里进行全局配置 -->
    <select id="findAll" resultType="user">select * from user</select>
</mapper>
```

## POJO

（User）

```java
public class User {
    private String username;
    private String password;
    //getter and setter
}
```

## Dao

（UserDao）

```java
import org.springframework.stereotype.Repository;

@Repository
public interface UserDao {
    public User findAll();
}
```



# 十二、总结

## 关于映射路径为/

当RequestMapping的path值为"/"时，会访问http://localhost:8080，如下：

```java
@RequestMapping(path="/")
public String index() {
    System.out.println("进入了/");
    return "test";
}
```

看起来他会渲染test.jsp，其实应该分为以下几种情况：

- 如果存在index.jsp文件，他会渲染index.jsp
- 如果不存在index.jsp文件，他会正常渲染test.jsp
- 如果存在index.jsp文件，但是在web.xml中指定欢迎文件路径为"/"，他会正常渲染test.jsp



