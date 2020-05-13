# Spring

# 一、Spring概述

## 1.1 Spring概述

1. Spring是一个轻量级的Java SE/EE开源框架，以IOC（Inverse Of Control：控制反转）和AOP（Aspect Oriented Programming：面向切面编程）为内核，使用基本的 JavaBean 完成以前只可能由 EJB 完成的工作，取代了 EJB 臃肿和低效的开发模式。
2. Spring的优势
   - 方便解耦，简化开发
   - AOP编程的支持
   - 申明式事务的支持
   - 方便程序的测试
   - 方便集成各种优秀框架
   - 降低Java EE API的使用难度
3. Spring体系结构：根据不同的功能，Spring模块大体可分为 Data Access/Integration、Web、AOP、Aspects、Messaging、Instrumentation、Core Container 和 Test，这些模块可以满足一切企业级应用开发的需求，一些模块的介绍如下：
   - Data Access/Integration（数据访问／集成）：包括 JDBC、ORM、OXM、JMS 和 Transactions 模块
   - Web模块：包括 Web、Servlet、Struts 和 Portlet 组件
   - Core Container（核心容器）：包括Beans 模块、Core 核心模块、Context 上下文模块和 Expression Language 表达式语言模块
   - 其他模块：包括AOP、Aspects、Instrumentation 以及 Test 模块

## 1.2 maven依赖

1. spring-context依赖

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.0.2.RELEASE</version>
   </dependency>
   ```

2. spring-core依赖

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-core</artifactId>
       <version>5.0.2.RELEASE</version>
   </dependency>
   ```

3. spring-beans依赖

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-beans</artifactId>
       <version>5.0.2.RELEASE</version>
   </dependency>
   ```


# 二、Spring IOC容器

## 2.1 IOC容器概述

1. IOC是指：在程序开发中，实例的创建不再由调用者管理，而是由 Spring 容器创建，Spring 容器会负责控制程序之间的关系，而不是由程序代码直接控制，因此，控制权由程序代码转移到了 Spring 容器中，控制权发生了反转，这就是 Spring 的 IoC 思想。
2. Spring 提供了两种 IoC 容器，为：
   - BeanFactory
   - ApplicationContext
3. BeanFactory 和 ApplicationContext 都是通过 XML 配置文件加载 Bean 的。BeanFactroy采用的是延迟加载形式来注入Bean的，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化，这样，我们就不能发现一些存在的Spring的配置问题；而ApplicationContext则相反，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误。 
4. 一般选择使用 ApplicationContext，而只有在系统资源较少时，才考虑使用 BeanFactory。BeanFactory是spring中比较原始的Factory，它不支持spring的许多插件，如AOP功能、Web应用、国际化等。而ApplicationContext接口，它由BeanFactory接口派生而来，因而BeanFactory拥有的功能它都有
5. 优点：
   - 降低了使用资源双方的依赖程度，即解耦
   - 提高的维护性和灵活性
   - 资源集中管理
   - 在实例化时不需要再去了解他的细节
6. 缺点：
   - 创建对象的步骤变复杂了，不直观
   - 效率上会有些损耗，当然这是值得的
   - 一旦类名被修改，还需要去xml文件中主动修改

## 2.2 BeanFactory

1. BeanFactory 是基础类型的 IoC 容器，由 org.springframework.beans.facytory.BeanFactory 接口定义

2. BeanFactory 就是一个管理 Bean 的工厂，它主要负责初始化各种 Bean，并调用它们的生命周期方法

3. BeanFactory 接口的常见实现类有：org.springframework.beans.factory.xml.XmlBeanFactory，是根据 XML 配置文件中的定义装配 Bean 的，其加载配置信息的代码为：

   ```java
   BeanFactory beanFactory = new XmlBeanFactory(new FileSystemResource("D://applicationContext.xml"));
   ```

## 2.3 ApplicationContext

1. ApplicationContext 是 BeanFactory 的子接口，也被称为应用上下文，全路径为 org.springframework.context.ApplicationContext，提供了 BeanFactory 的所有功能，还添加了对 i18n（国际化）、资源访问、事件传播等方面的良好支持

2. ApplicationContext 接口有两个常用的实现类，具体如下：

   - ClassPathXmlApplicationContext：从类路径 ClassPath 中寻找指定的 XML 配置文件，找到并装载完成 ApplicationContext 的实例化工作

   ```java
   ApplicationContext applicationContext = new ClassPathXmlApplicationContext(String configLocation);	//configLocation 参数用于指定 Spring 配置文件的名称和位置，如 applicationContext.xml
   ```

   - FileSystemXmlApplicationContext：从指定的文件系统路径中寻找指定的 XML 配置文件，找到并装载完成 ApplicationContext 的实例化工作

   ```java
   ApplicationContext applicationContext = new FileSystemXmlApplicationContext(String configLocation);	//configLocation 参数用于指定配置文件的位置，它可以获取类路径之外的资源，如“F：/workspaces/applicationContext.xml”。
   ```

3. 一般来说，普通的Java项目会使用xml配置形式来加载容器，而Web服务器通常使用web.xml文件实例化ApplicationContext 容器， 即使用基于 ContextLoaderListener 实现的方式：

  ```xml
  <!--指定Spring配置文件的位置，有多个配置文件时，以逗号分隔-->
  <context-param>
      <param-name>contextConfigLocation</param-name>
      <!--spring将加载spring目录下的applicationContext.xml文件-->
      <param-value>
          classpath:spring/applicationContext.xml
      </param-value>
  </context-param>
  <!--指定以ContextLoaderListener方式启动Spring容器-->
  <listener>
      <listener-class>
          org.springframework.web.context.ContextLoaderListener
      </listener-class>
  </listener>
  ```

## 2.4 Spring IOC代码示例

1. Spring 的核心配置文件 applicationContext.xml（或bean.xml）：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">
       <!-- 由 Spring容器创建该类的实例对象 -->
       <bean id="person" class="com.jzp.Person" />
   </beans>
   ```

   可以通过id来获取实例，class属性为Bean类的全限定类名（包名+类名）

2. 测试：

   ```java
   // 定义Spring配置文件的路径，主目录下
   String xmlPath = "applicationContext.xml";
   // 初始化Spring容器，加载配置文件
   ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
   // 通过容器获取personDao实例
   Person person = (Person) applicationContext.getBean("person");
   ```



# 三、Spring Bean

## 3.1 bean元素相关属性

| 属性名称        | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| id              | 是一个 Bean 的唯一标识符，Spring 容器对 Bean 的配置和管理都通过该属性完成 |
| name            | Spring 容器同样可以通过此属性对容器中的 Bean 进行配置和管理，name 属性中可以为 Bean 指定多个名称，每个名称之间用逗号或分号隔开 |
| class           | 该属性指定了 Bean 的具体实现类，它必须是一个完整的类名，使用类的全限定名 |
| scope           | 用于设定 Bean 实例的作用域，其属性值有 singleton（单例）、prototype（原型）、request、session 和 global Session。其默认值是 singleton |
| constructor-arg | <bean>元素的子元素，可以使用此元素传入构造参数进行实例化。该元素的 index 属性指定构造参数的序号（从 0 开始），type 属性指定构造参数的类型 |
| property        | <bean>元素的子元素，用于调用 Bean 实例中的 Set 方法完成属性赋值，从而完成依赖注入。该元素的 name 属性指定 Bean 实例中的相应属性名 |
| ref             | <property> 和 <constructor-arg> 等元素的子元索，该元素中的 bean 属性用于指定对 Bean 工厂中某个 Bean 实例的引用 |
| value           | <property> 和 <constractor-arg> 等元素的子元素，用于直接指定一个常量值 |
| list            | 用于封装 List 或数组类型的依赖注入                           |
| set             | 用于封装 Set 类型属性的依赖注入                              |
| map             | 用于封装 Map 类型属性的依赖注入                              |
| entry           | <map> 元素的子元素，用于设置一个键值对。其 key 属性指定字符串类型的键值，ref 或 value 子元素指定其值 |



## 3.2 Bean的实例化

1. Bean有三种实例化方式
   - 构造器实例化：通过 Bean 对应的类中默认的构造函数实例化 Bean。
   - 静态工厂方式实例化：需要提供一个静态工厂方法创建 Bean 的实例
   - 实例工厂方式实例化：工厂类不再使用静态方法创建 Bean 的实例，而是直接在成员方法中创建 Bean 的实例。

2. 构造器实例化

   - 创建实体类

   ```java
   public class Person{}
   ```

   - Spring 配置文件<bean>

   ```xml
   <bean id="person" class="com.jzp.Person" />
   ```

3. 静态工厂方式实例化

   - 创建静态工厂方法类

   ```java
   public class MyBeanFactory {
       // 创建Bean实例的静态工厂方法
       public static Person createBean() {
           return new Person();
       }
   }
   ```

   - Spring 配置文件<bean>

   ```xml
   <bean id="person" class="com.jzp.MyBeanFactory" factory-method="createBean" />
   ```

4. 实例工厂方式实例化

   - 创建实例工厂类

   ```java
   public class MyBeanFactory {
       public MyBeanFactory() {
           System.out.println("person3工厂实例化中");
       }
       // 创建Bean的方法
       public Person createBean() {
           return new Person();
       }
   }
   ```

   - Spring 配置文件<bean>

   ```xml
   <bean id="myBeanFactory" class="com.jzp.MyBeanFactory" />
   <!-- factory-bean属性指定一个实例工厂，factory-method属性确定使用工厂中的哪个方法 -->
   <bean id="person" factory-bean="myBeanFactory" factory-method="createBean" />
   ```



## 3.3 Bean的作用域

1. 作用域的种类

   - singleton：单例模式，Bean在Spring容器中只有一个实例，这是Bean默认的作用域；对于无状态会话（Dao，Service），建议使用singleton
   - prototype：原型模式，每次通过Spring容器获取Bean时，都将创建一个新的Bean实例；对需要保持会话状态的 Bean，建议使用prototype
   - request：在一次HTTP请求中，Spring容器会返回该Bean的同一个实例；而对不同的HTTP请求，会返回不同的实例，该作用域只在当前HTTP请求内有效
   - session：在一次HTTP Session中，Spring容器会返回该Bean的同一个实例；而对不同的HTTP请求，会返回不同的实例，该作用域只在当前HTTP Session内有效
   - global session：在一个全局的 HTTP Session 中，容器会返回该 Bean 的同一个实例。该作用域仅在使用 portlet context 时有效

2. 修改作用域

   ```xml
   <bean id="person" class="com.mengma.scope.Person" scope="prototype"/>
   ```

3. 当获取Bean实例时，不同的作用域会创建不同的实例，如：

   ```java
   applicationContext.getBean("person") == applicationContext.getBean("person")
   // 对于singleton来说，两者相等
   // 对于prototype来说，两者不相等
   ```



## 3.4 Bean的生命周期

1. 在singleton作用域下，Spring能精准知道该Bean何时被创建，何时初始化完成，何时注销
2. 在prototype作用域下，Spring只负责创建，之后就交给客户端代码管理，不会跟踪其生命周期

3. 了解 Spring 生命周期的意义就在于，可以利用 Bean 在其存活期间的指定时刻完成一些相关操作。这种时刻可能有很多，但一般情况下，会在 Bean 被初始化后和被销毁前执行一些相关操作。

4. Bean 的生命周期是一个很复杂的执行过程，我们可以利用 Spring 提供的方法定制 Bean 的创建过程。

5. 而 Spring 容器在保证一个 Bean 能够使用之前，会进行很多工作，周期如下：

   1）根据配置情况调用 Bean 构造方法或工厂方法实例化 Bean。

   2）利用依赖注入完成 Bean 中所有属性值的配置注入。

   3）如果 Bean 实现了 BeanNameAware 接口，则 Spring 调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值。

   4）如果 Bean 实现了 BeanFactoryAware 接口，则 Spring 调用 setBeanFactory() 方法传入当前工厂实例的引用。

   5）如果 Bean 实现了 ApplicationContextAware 接口，则 Spring 调用 setApplicationContext() 方法传入当前 ApplicationContext 实例的引用。

   6）如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 AOP 就是利用它实现的。

   7）如果 Bean 实现了 InitializingBean 接口，则 Spring 将调用 afterPropertiesSet() 方法。

   8）如果在配置文件中通过 init-method 属性指定了初始化方法，则调用该初始化方法。

   9）如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的初始化方法 postProcessAfterInitialization()。此时，Bean 已经可以被应用系统使用了。

   10）如果在 <bean> 中指定了该 Bean 的作用范围为 scope="singleton"，则将该 Bean 放入 Spring IoC 的缓存池中，将触发 Spring 对该 Bean 的生命周期管理；如果在 <bean> 中指定了该 Bean 的作用范围为 scope="prototype"，则将该 Bean 交给调用者，调用者管理该 Bean 的生命周期，Spring 不再管理该 Bean。

   11）如果 Bean 实现了 DisposableBean 接口，则 Spring 会调用 destory() 方法将 Spring 中的 Bean 销毁；如果在配置文件中通过 destory-method 属性指定了 Bean 的销毁方法，则 Spring 将调用该方法对 Bean 进行销毁。

6. 通过实现特定的接口或 <bean> 的属性设置，都可以对 Bean 的生命周期过程产生影响。虽然可以随意配置 <bean> 的属性，但是建议不要过多地使用 Bean 实现接口，因为这样会导致代码和 Spring 的聚合过于紧密

## 3.5 Bean的装配

1. Bean 的装配可以理解为依赖关系注入，Bean 的装配方式也就是 Bean 的依赖注入方式。

2. Bean 的装配一般用于Bean变量的装配，即实例化

3. Spring支持多形式的装配方式：

   - 基于 XML 的 Bean 装配
   - 基于 Annotation 的 Bean 装配
   - 自动装配

4. 基于 XML 的 Bean 装配

   有两种方式，即设值注入和构造注入，设值注入要求一个Bean必须满足亮要求：

   - 必须提供一个默认的无参构造方法。
   - 必须为需要注入的属性提供对应的 setter 方法。

   使用设值注入时，在 Spring 配置文件中，需要使用 <bean> 元素的子元素 <property> 元素为每个属性注入值

   使用构造注入时，在配置文件中，主要使用 <constructor-arg> 标签定义构造方法的参数，可以使用其 value 属性（或子元素）设置该参数的值

   在使用XML装配的时候，需要指定name和value|ref，并通过value|ref来寻找对应的id，而name对应的是Bean中的属性（全局变量）

   - 实体类

   ```java
   public class Person {
       private String name;
       public String getName() {
           return name;
       }
       public void setName(String name) {
           this.name = name;
       }
       // 默认无参的构造方法
       public Person() {
           super();
       }
       // 有参的构造方法
       public Person(String name) {
           super();
           this.name = name;
       }
   }
   ```

   - 配置文件

   ```xml
   <!-- 使用设值注入方式装配Person实例 -->
   <bean id="person1" class="com.jzp.Person">
       <property name="name" value="zhangsan" />
   </bean>
   <!-- 使用构造方法装配Person实例 -->
   <bean id="person2" class="com.jzp.Person">
       <constructor-arg index="0" value="lisi" />
   </bean>
   ```

5. 基于 Annotation 的 Bean 装配

   如果应用中Bean的数量过多，会导致XML配置文件过于臃肿，此时可以使用注解

   常用注解如下：

   - @Component：注解Bean类
   - @Repository：注解Dao
   - @Service：注解Service
   - @Controller：注解控制层
   - @Autowired：对 Bean 的属性变量、属性的 Set 方法及构造函数进行标注
   - @Resource：作用与 Autowired 一样，@Resource 默认按照 Bean 实例名称进行装配，@Resource 中有两个重要属性：name 和 type。Spring 将 name 属性解析为 Bean 实例名称，type 属性解析为 Bean 实例类型。如果指定 name 属性，则按实例名称进行装配；如果指定 type 属性，则按 Bean 类型进行装配。如果都不指定，则先按 Bean 实例名称装配，如果不能匹配，则再按照 Bean 类型进行装配；如果都无法匹配，则抛出 NoSuchBeanDefinitionException 异常。
   - @Qualifier：与 @Autowired 注解配合使用，会将默认的按 Bean 类型装配修改为按 Bean 的实例名称装配，Bean 的实例名称由 @Qualifier 注解的参数指定

   例如：

   ```java
   @Repository("personDao")
   public class PersonDao{
       public void add() {
           System.out.println("Dao被执行");
       }
   }
   ```

   ```java
   @Repository("personService")
   public class Service{
       @Resource(name = "personDao")	//这个注解可以直接实例化personDao，name为@Repository指定的名字，可以不写name=
       private PersonDao personDao;
       
       public PersonDao execPersonDao() {
           personDao.add();
       }
   }
   ```

   ```java
   ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
   PersonDao personDao = (PersonDao) applicationContext.getBean("personDao");
   personDao.add();
   ```

   同时，要在applicationContext.xml文件中写入上下文组件扫描，定位到有注解的包

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
       <context:component-scan base-package="com.jzp.personpack"/>
   </beans>
   ```

   

6. Spring自动装配

   即自动通过Bean中的属性名来从其他<Bean>标签里面去寻找相应的值或bean，可以根据name来找id，也可以字节通过类型来寻找，这些可以由autowire属性来控制，autowire有如下值：

   | 名称        | 说明                                                         |
   | ----------- | ------------------------------------------------------------ |
   | byName      | 根据 Property 的 name 自动装配，如果一个 Bean 的 name 和另一个 Bean 中的 Property 的 name 相同，则自动装配这个 Bean 到 Property 中。 |
   | byType      | 根据 Property 的数据类型（Type）自动装配，如果一个 Bean 的数据类型兼容另一个 Bean 中 Property 的数据类型，则自动装配。此时可以不需要id。 |
   | constructor | 根据构造方法的参数的数据类型，进行 byType 模式的自动装配。   |
   | autodetect  | 如果发现默认的构造方法，则用 constructor 模式，否则用 byType 模式。 |
   | no          | 默认情况下，不使用自动装配，Bean 依赖必须通过 ref 元素定义。 |

   实例：

   ```xml
   <bean id="cat" class="com.jzp.Cat"></bean>
   <bean id="dog" class="com.jzp.Dog"></bean>
   <bean id="Animal" 
         class="com.jzp.Animal"
         autowire="byName">
   </bean>
   ```

   

# 四、Spring AOP

## 4.1 AOP概述

1. 面向切面编程（AOP）和面向对象编程（OOP）类似，也是一种编程模式，而Spring AOP 是基于 AOP 编程模式的一个框架，它的使用有效减少了系统间的重复代码，达到了模块间的松耦合目的。
2. AOP即面向切面编程，它将业务逻辑的各个部分进行隔离，使开发人员在编写业务逻辑时可以专心于核心业务，从而提高了开发效率。他采取横向抽取机制，取代了传统纵向继承体系的重复性代码，其应用主要体现在事务处理、日志管理、权限控制、异常处理等方面。
3. 目前流行的AOP框架有两个，即Spring AOP 和 AspectJ，Spring采用的是动态代理织入，而AspectJ采用编译期织入和类装载期织入。
4. 一些AOP术语：
   - Aspect（切面）：切面由切点和增强(或引介)组成，或者只由增强（或引介）实现。
   - Joinpoint（连接点）：连接点就是程序执行的某个特定的位置，如：类开始初始化前、类初始化后、类的某个方法调用前、类的某个方法调用后、方法抛出异常后等。Spring 只支持类的方法前、后、抛出异常后的连接点。
   - Pointcut（切入点）：一个项目中有很多的类，一个类有很多个连接点，当我们需要在某个方法前插入一段增强（advice）代码时，我们就需要使用切点信息来确定，要在哪些连接点上添加增强。
   - Advice（通知）：AOP（切面编程）是用来给某一类特殊的连接点，添加一些特殊的功能
   - Target（目标）：目标对象就是我们需要对它进行增强的业务
   - Proxy（代理）：一个类被AOP织入后生成出了一个结果类，它是融合了原类和增强逻辑的代理类。
   - Weaving（织入）：织入就是将增强添加到目标类具体连接点上的过程，有以下织入：
     - 编译期织入：这要求使用特殊java编译器
     - 类装载期织入：这要求使用特殊的类装载器
     - 动态代理织入：在运行期为目标类添加增强生成子类的方式

## 4.2 Spring通知概述

1. 通知（Advice）其实就是对目标切入点进行增强的内容，Spring AOP 为通知（Advice）提供了接口`org.aopalliance.aop.Advice`。
2. 按照在目标类方法的连接点位置，通知可分为以下五种类型，环绕通知属于`org.aopalliance.intercept`包下，其他的属于`org.springframework.aop`包下；Spring AOP提供了以下5中接口，使用相应的通知就需要实现相应的接口
   - 前置通知（MethodBeforeAdvice）：在方法之前自动执行的通知称为前置通知，可以应用于权限管理等功能。
   - 后置通知（AfterReturningAdvice）：在方法之后自动执行的通知称为后置通知，可以应用于关闭流、上传文件、删除临时文件等功能。
   - 环绕通知（MethodInterceptor）：在方法前后自动执行的通知称为环绕通知，可以应用于日志、事务管理等功能。
   - 异常通知（ThrowsAdvice）： 在方法抛出异常时自动执行的通知称为异常通知，可以应用于处理异常记录日志等功能。
   - 引介通知（IntroductionInterceptor）：在目标类中添加一些新的方法和属性，可以应用于修改旧版本程序（增强类）。
3. 创建AOP代理是使用 org.springframework.aop.framework.ProxyFactoryBean，这个类对应的切入点和通知提供了完整的控制能力，并可以生成指定的内容；常用配置属性有：
   - target：代理的目标对象
   - proxyInterfaces：代理要实现的接口对象
   - proxyTargetClass：是否对类代理而不是接口，设置为 true 时，使用 CGLIB 代理
   - interceptorNames：需要植入目标的 Advice的目标对象
   - singleton：返回的代理是否为单例，默认为 true（返回单实例）
   - target：代理的目标对象
   - optimize：当设置为 true 时，强制使用 CGLIB

## 4.3 Spring通知-代码演示

1. 切面类演示：

   ```java
   import org.aopalliance.intercept.MethodInterceptor;
   import org.aopalliance.intercept.MethodInvocation;
   import org.springframework.aop.AfterReturningAdvice;
   import org.springframework.aop.IntroductionInterceptor;
   import org.springframework.aop.MethodBeforeAdvice;
   import org.springframework.aop.ThrowsAdvice;
   import org.springframework.stereotype.Component;
   import java.lang.reflect.Method;
   
   //环绕通知
   public class SpringAspect implements MethodInterceptor {
       @Override
       public Object invoke(MethodInvocation invocation) throws Throwable {
           System.out.println("方法执行之前");
           Object proceed = invocation.proceed();
           System.out.println("方法执行之后");
           return proceed;
       }
   }
   
   //前置通知
   public class SpringAspect2 implements MethodBeforeAdvice {
       @Override
       public void before(Method method, Object[] args, Object target) throws Throwable {
           System.out.println("方法执行之前");
       }
   }
   ```

2. 配置applicationContext.xml

   ```xml
   <!--目标类 -->
   <bean id="customerDao" class="com.jzp.jdkproxy.CustomerDaoImpl" />
   <!-- 通知 advice -->
   <bean id="SpringAspect" class="com.jzp.jdkproxy.SpringAspect" />
   <!--生成代理对象 -->
   <bean id="customerDaoProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
       <!--代理实现的接口 -->
       <property name="proxyInterfaces" value="com.jzp.jdkproxy.CustomerDao" />
       <!--代理的目标对象 -->
       <property name="target" ref="customerDao" />
       <!--用通知增强目标 -->
       <property name="interceptorNames" value="SpringAspect" />
       <!-- 如何生成代理，true:使用cglib; false :使用jdk动态代理 -->
       <property name="proxyTargetClass" value="true" />
   </bean>
   ```

3. 测试：

   ```java
   ClassPathXmlApplicationContext appContext = new ClassPathXmlApplicationContext("bean.xml");
   CustomerDao customerDao = (CustomerDao) appContext.getBean("customerDaoProxy");
   customerDao.add();
   ```

## 4.4 AspectJ概述

1. AspectJ是一个基于Java语言的AOP框架，Spring2.0新增了对AspectJ的支持，建议使用AspectJ开发AOP

2. 使用 AspectJ 开发 AOP 通常有两种方式：

   - 基于 XML 的声明式。
   - 基于 Annotation 的声明式。

3. maven依赖

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

## 4.5 AspectJ-基于xml实现

1. 切面类

   ```java
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.ProceedingJoinPoint;
   
   public class MyAspect {
       // 前置通知
       public void myBefore(JoinPoint joinPoint) {
           System.out.print("前置通知，目标：");
           System.out.print(joinPoint.getTarget() + "方法名称:");
           System.out.println(joinPoint.getSignature().getName());
       }
       // 环绕通知
       public Object myAround(ProceedingJoinPoint proceedingJoinPoint)
               throws Throwable {
           System.out.println("环绕开始"); // 开始
           Object obj = proceedingJoinPoint.proceed(); // 执行当前目标方法
           System.out.println("环绕结束"); // 结束
           return obj;
       }
   
       public void myAfterReturning(JoinPoint joinPoint) {}// 后置通知
       public void myAfterThrowing(JoinPoint joinPoint, Throwable e) {}// 异常通知
       public void myAfter() {}// 最终通知
       
   }
   ```

2. applicationContext.xml配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 					xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
       <!--目标类 -->
       <bean id="customerDao" class="com.jzp.jdkproxy.CustomerDaoImpl" />
       <!--切面类 -->
       <bean id="myAspectJ" class="com.jzp.jdkproxy.MyAspectJ"></bean>
       <aop:config>
           <aop:aspect ref="myAspect">
               <!-- 配置切入点，通知最后增强哪些方法 -->
               <aop:pointcut id="myPointCut" expression="execution(* com.jzp.jdkproxy.CustomerDaoImpl (..))"/>
               <!--前置通知，关联通知 Advice和切入点PointCut -->
               <aop:before method="myBefore" pointcut-ref="myPointCut"/>
               <!--后置通知 -->
               <aop:after-returning method="myAfterReturning" pointcut-ref="myPointCut"/>
               <!--环绕通知 -->
               <aop:around method="myAround" pointcut-ref="myPointCut" />
               <!--抛出通知：用于处理程序发生异常，可以接收当前方法产生的异常 -->
               <!-- *注意：如果程序没有异常，则不会执行增强 -->
               <!-- * throwing属性：用于设置通知第二个参数的名称，类型Throwable -->
               <aop:after-throwing method="myAfterThrowing"
                                   pointcut-ref="myPointCut" throwing="e" />
               <!--最终通知：无论程序发生任何事情，都将执行 -->
               <aop:after method="myAfter" pointcut-ref="myPointCut" />
           </aop:aspect>
       </aop:config>
   </beans>
   ```

3. 测试

   ```java
   ClassPathXmlApplicationContext appContext = new ClassPathXmlApplicationContext("bean.xml");
   CustomerDao customerDao = (CustomerDao) appContext.getBean("customerDao");
   customerDao.add();
   customerDao.update();
   customerDao.delete();
   customerDao.find();
   ```

## 4.6 AspectJ-基于Annotation实现

1. AspectJ有以下注解：
   - @Aspect：用于定义一个切面。
   - @Before：用于定义前置通知，相当于 BeforeAdvice。
   - @AfterReturning：用于定义后置通知，相当于 AfterReturningAdvice。
   - @Around：用于定义环绕通知，相当于MethodInterceptor。
   - @AfterThrowing：用于定义抛出通知，相当于ThrowAdvice。
   - @After：用于定义最终final通知，不管是否异常，该通知都会执行。
   - @DeclareParents：用于定义引介通知，相当于IntroductionInterceptor（不要求掌握）

2. 切面类

   ```java
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.ProceedingJoinPoint;
   import org.aspectj.lang.annotation.*;
   import org.springframework.stereotype.Component;
   
   @Aspect
   @Component
   public class MyAspectJAno {
       // 要求：方法必须是private，没有值，名称自定义，没有参数
       @Pointcut("execution ( * com.jzp.jdkproxy.CustomerDaoImpl.*(..))")
       private void myPointCut() {}
   
       // 前置通知
       @Before("myPointCut()")
       public void myBefore(JoinPoint joinPoint) {
           System.out.print("前置通知，目标：");
           System.out.print(joinPoint.getTarget() + "方法名称:");
           System.out.println(joinPoint.getSignature().getName());
       }
       // 后置通知
       @AfterReturning(value = "myPointCut()")
       public void myAfterReturning(JoinPoint joinPoint) {
           System.out.print("后置通知，方法名称：" + joinPoint.getSignature().getName());
       }
       // 环绕通知
       @Around("myPointCut()")
       public Object myAround(ProceedingJoinPoint proceedingJoinPoint)
               throws Throwable {
           System.out.println("环绕开始"); // 开始
           Object obj = proceedingJoinPoint.proceed(); // 执行当前目标方法
           System.out.println("环绕结束"); // 结束
           return obj;
       }
       // 异常通知
       @AfterThrowing(value = "myPointCut()", throwing = "e")
       public void myAfterThrowing(JoinPoint joinPoint, Throwable e) {
           System.out.println("异常通知" + "出错了" + e.getMessage());
       }
       // 最终通知
       @After("myPointCut()")
       public void myAfter() {
           System.out.println("最终通知");
       }
   }
   ```

3. applicationContext.xml配置文件

   - 给目标类com.jzp.jdkproxy.CustomerDaoImpl添加注解 @Repository（"customerDao"）

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/aop
                              http://www.springframework.org/schema/aop/spring-aop.xsd
                              http://www.springframework.org/schema/context
                              http://www.springframework.org/schema/context/spring-context.xsd">
       <!--<bean id="user" class="com.jzp.ioc.User" autowire="byName"/>-->
       <!--扫描含com.jzp.jdkproxy包下的所有注解-->
       <context:component-scan base-package="com.jzp.jdkproxy"/>
       <!-- 使切面开启自动代理 -->
       <aop:aspectj-autoproxy/>
   </beans>
   ```

4. 开发过程中建议使用注解方式来实现AspectJ通知



# 五、Spring持久化

## 5.1 JDBCTemplate

1. JDBCTemplate是Spring 对 JDBC 支持的核心类，它提供了所有对数据库操作功能的支持。

2. JdbcTemplate 类继承自抽象类 JdbcAccessor，同时实现了 JdbcOperations 接口。其直接父类 JdbcAccessor 为子类提供了一些访问数据库时使用的公共属性

3. JDBCTemplate提供了以下3个公共属性

   - DataSource：获取数据库连接，具体实现时还可以引入对数据库连接的缓冲池和分布式事务的支持，它可以作为访问数据库资源的标准接口。
   - SQLExceptionTranslator：负责对 SQLException 进行转译工作。通过必要的设置或者获取 SQLExceptionTranslator 中的方法，可以使 JdbcTemplate 在需要处理 SQLException 时，委托 SQLExceptionTranslator 的实现类完成相关的转译工作。
   - JdbcOperations：定义了在 JdbcTemplate 类中可以使用的操作集合，包括添加、修改、查询和删除等操作。

4. dataSource中，定义了四个连接数据库的属性，如下：

   - driverClassName：所使用的驱动名称，对应驱动 JAR 包中的 Driver 类
   - url：数据源所在地址
   - username：访问数据库的用户名
   - password：访问数据库的密码

5. JDBCTemplate maven配置

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>${spring.version}</version>
   </dependency>
   ```

6. JDBC相关信息是在Spring中配置完成的，模板如下：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http:/www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd"> 
   
       <!-- 配置数据源 --> 
       <bean id="dataSource" class="org.springframework.jdbc.dataSource.DriverManagerDataSource">
           <!--数据库驱动-->
           <property name="driverClassName" value="com.mysql.jdbc.Driver" /> 
           <!--连接数据库的url-->
           <property name= "url" value="jdbc:mysql://localhost:3306/test" />
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
       <!--配置注入类-->
       <bean id="jdbcTemplate" class="com.jzp.jdkproxy.CustomerDaoImpl">
           <property name="jdbcTemplate" ref="jdbcTemplate"/>
       </bean>
   </beans>
   ```

7. 也可以将DataSource信息写在properties中，如下：

   - db.properties

   ```properties
   # 对于jdbcTemplate
   db.driverClassName=com.mysql.jdbc.Driver
   db.url=jdbc:mysql://localhost:3306/test
   db.username=root
   db.password=root
   
   # 对于c3p0
   db.driverClass = com.mysql.jdbc.Driver
   db.jdbcUrl = jdbc:mysql://localhost:3306/test
   db.user = root
   db.password = root
   ```

   - 配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd  
               http://www.springframework.org/schema/context
               http://www.springframework.org/schema/context/spring-context.xsd
               http://www.springframework.org/schema/tx
               http://www.springframework.org/schema/tx/spring-tx-2.5.xsd
               http://www.springframework.org/schema/aop
               http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
       <!-- 加载properties文件 -->
       <context:property-placeholder location="classpath:db.properties" />
       <!-- 配置数据源，读取properties文件信息 -->
       <bean id="dataSource" class="org.springframework.jdbc.dataSource.DriverManagerDataSource">
           <property name="driverClass" value="${jdbc.driverClass}" />
           <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />
           <property name="user" value="${jdbc.user}" />
           <property name="password" value="${jdbc.password}" />
       </bean>
       <!--配置JDBC模板-->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.jdbcTemplate">
           <!--默认必须使用数据源-->
           <property name="dataSource" ref="dataSource"/>
       </bean>
       <!--配置注入类-->
       <bean id="jdbcTemplate" class="com.jzp.jdkproxy.CustomerDaoImpl">
           <property name="jdbcTemplate" ref="jdbcTemplate"/>
       </bean>
   </beans>
   ```

## 5.2 事务管理

1. Spring 的事务管理是基于 AOP 实现的，而 AOP 是以方法为单位的，在 Java EE 开发经常采用的分层模式中，Spring 的事务处理位于业务逻辑层，它提供了针对事务的解决方案。
2. Spring的事务管理有两种方式：
   - 编程式事务管理：即传统的事务管理方式，通过编写代码实现事务管理
   - 声明式事务管理：基于AOP技术实现的事务管理，无需通过编程的方式管理事务，只需要在配置文件中进行相应的规则说明，就可以将事务规则应用到业务逻辑中；声明式事务管理主要有两种方式：
     - 基于 XML 方式的声明式事务管理。
     - 通过 Annotation 注解方式的事务管理。
3. Spring 的事务属性为：
   - 传播行为
   - 隔离级别
   - 只读
   - 超时
4. Spring事务管理的核心接口为：
   - PlatformTransactionManager
   - TransactionDefinition 
   - TransactionStatus
5. PlatformTransactionManager 接口是 Spring 提供的平台事务管理器，用于管理事务。Spring 将 xml 中配置的事务详细信息封装到对象 TransactionDefinition 中，然后通过事务管理器的 getTransaction() 方法获得事务的状态（TransactionStatus），并对事务进行下一步的操作。该接口中提供了三个事务操作方法，具体如下：
   - TransactionStatus getTransaction（TransactionDefinition definition）：用于获取事务状态信息。
   - void commit（TransactionStatus status）：用于提交事务。
   - void rollback（TransactionStatus status）：用于回滚事务。
6. TransactionDefinition 接口是事务定义（描述）的对象，它提供了事务相关信息获取的方法，其中包括五个操作，具体如下：
   - String getName()：获取事务对象名称。
   - int getIsolationLevel()：获取事务的隔离级别。
   - int getPropagationBehavior()：获取事务的传播行为。
   - int getTimeout()：获取事务的超时时间。
   - boolean isReadOnly()：获取事务是否只读。
7. TransactionStatus 接口是事务的状态，它描述了某一时间点上事务的状态信息。其中包含六个操作：
   - void flush()：刷新事务
   - boolean hasSavepoint()：获取是否存在保存点
   - boolean isCompleted()：获取事务是否完成
   - boolean isNewTransaction()：获取是否是新事务
   - booleanisRollbackOnly()：获取是否回滚
   - void setRollbackOnly()：设置事务回滚
8. Spring事务的隔离级别
   - 默认隔离级别（Default）：使用数据库设置的隔离级别(默认),由DBA默认的设置来决定隔离级别
   - 读未提交数据（read uncommited）：有的事务都可以“看到”未提交事务的执行结果；会导致脏读、不可重复读和幻读的问题的出现
   - 读已提交数据（read commited）：一个事务在开始时，只能"看见"已提交事务所做的改变；Oracle和sql server默认的级别；可以避免脏读，但不可重复读和幻读问题仍然会出现
   - 可重复读（Repeatable read）：确保同一事务的多个实例在并发读取数据时，会看到同样的行；           事务在读取某数据的瞬间（就是开始读取的瞬间），必须先对其加 行级共享锁，直到事务结束才释放；事务在更新某数据的瞬间（就是发生更新的瞬间），必须先对其加 行级排他锁，直到事务结束才释放；
   - 可串行化（Serializable）：最高的事务隔离级别，每个读的数据上加上共享锁
     事务在读取数据时，必须先对其加 表级共享锁 ，直到事务结束才释放；
     事务在更新数据时，必须先对其加 表级排他锁 ，直到事务结束才释放
9. Spring事务的传播属性
   - propagation_required：支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择，也是Spring的默认传播属性
   - propagation_supports：支持当前事务，如果当前没有事务，就以非事务方式执行
   - propagation_mandatory：支持当前事务，如果当前没有事务，就抛出异常
   - propagation_requires_new：新建事务，如果当前存在事务，把当前事务挂起
   - propagation_not_supported：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起
   - propagation_never：以非事务方式执行，如果当前存在事务，则抛出异常
   - propagation_nested：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则进行与propagation_required类似的操作

## 5.3 基于xml的事务管理

1.  Spring配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd  
               http://www.springframework.org/schema/context
               http://www.springframework.org/schema/context/spring-context.xsd
               http://www.springframework.org/schema/tx
               http://www.springframework.org/schema/tx/spring-tx-2.5.xsd
               http://www.springframework.org/schema/aop
               http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
   
       <!-- 加载properties文件 -->
       <context:property-placeholder location="classpath:c3p0-db.properties" />
       <!-- 配置数据源，读取properties文件信息 -->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="${jdbc.driverClass}" />
           <property name="jdbcUrl" value="${jdbc.jdbcUrl}" />
           <property name="user" value="${jdbc.user}" />
           <property name="password" value="${jdbc.password}" />
       </bean>
       
       <!-- 配置jdbc模板 -->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <property name="dataSource" ref="dataSource" />
       </bean>
       <!-- 配置dao -->
       <bean id="accountDao" class="com.mengma.dao.impl.AccountDaoImpl">
           <property name="jdbcTemplate" ref="jdbcTemplate" />
       </bean>
       <!-- 配置service -->
       <bean id="accountService" class="com.mengma.service.impl.AccountServiceImpl">
           <property name="accountDao" ref="accountDao" />
       </bean>
       
       <!-- 事务管理器，依赖于数据源 -->
       <bean id="txManager"
           class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource" />
       </bean>
       
       <!-- 编写通知：对事务进行增强（通知），需要编写切入点和具体执行事务的细节 -->
       <tx:advice id="txAdvice" transaction-manager="txManager">
           <tx:attributes>
               <!-- 给切入点方法添加事务详情，name表示方法名称，*表示任意方法名称，propagation用于设置传播行为，read-only表示隔离级别，是否只读 -->
               <tx:method name="find*" propagation="SUPPORTS"
                   rollback-for="Exception" />
               <tx:method name="*" propagation="REQUIRED" isolation="DEFAULT"
                   read-only="false" />
           </tx:attributes>
       </tx:advice>
       
       <!-- aop编写，让Spring自动对目标生成代理，需要使用AspectJ的表达式 -->
       <aop:config>
           <!-- 切入点 -->
           <aop:pointcut expression="execution(* com.mengma.service.*.*(..))"
               id="txPointCut" />
           <!-- 切面：将切入点与通知整合 -->
           <aop:advisor pointcut-ref="txPointCut" advice-ref="txAdvice" />
       </aop:config>
   </beans>
   ```

   

## 5.4 基于注解的事务管理

1. Spring配置文件：将<tx:advice>和<aop:config>节点替换为以下：

   ```xml
   <!-- 注册事务管理驱动 -->
   <tx:annotation-driven transaction-manager="txManager"/>
   ```

2. 接着在需要添加事务的类上面进行注解

   ```java
   @Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, readOnly = false)
   public class AccountServiceImpl {}
   ```

   

# 六、maven 配置

1. Spring基本配置

   ```xml
   <properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
       <spring.version>5.0.2.RELEASE</spring.version>
   </properties>
   
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
   ```

2. jdbcTemplate配置

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>${spring.version}</version>
   </dependency>
   ```

3. Servlet和jsp配置

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

4. mysql配置

   ```xml
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.5</version>
   </dependency>
   ```

5. c3p0配置

   ```xml
   <dependency>
       <groupId>com.mchange</groupId>
       <artifactId>c3p0</artifactId>
       <version>0.9.5.2</version>
   </dependency>
   ```

6. cglib配置

   ```xml
   <dependency>
       <groupId>cglib</groupId>
       <artifactId>cglib</artifactId>
       <version>3.2.5</version>
   </dependency>
   ```

7. AspectJ配置

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







