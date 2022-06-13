## Spring

### 1. 使用Spring框架的好处？

- 轻量：Spring 是轻量的，基本的版本大约2MB。
- **IOC(控制反转)** ：将对象的创建权，由Spring管理。对象们给出它们的依赖，而不是创建或查找依赖的对象们。
- **DI(依赖注⼊)** ：在Spring创建对象的过程中，把对象依赖的属性注⼊到类中。
- 面向切面的编程(AOP)：Spring支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
- 容器：Spring 包含并管理应用中对象的生命周期和配置。
- MVC框架：Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。
- 事务管理：Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务 （JTA）。
- 异常处理：Spring 提供方便的API把具体技术相关的异常（比如由JDBC，Hibernate等抛出的）转化为一致的unchecked异常。



### 2. Spring框架的七⼤模块

* Spring Core：框架的最基础部分，提供 IoC 容器，对 bean 进⾏管理。

* Spring Context：继承BeanFactory，提供上下⽂信息，扩展出JNDI、EJB、电⼦邮件、国际化等功能。

- Spring DAO：提供了JDBC的抽象层，还提供了声明性事务管理⽅法。
- Spring ORM：提供了JPA、JDO、Hibernate、MyBatis 等ORM映射层。
- Spring AOP：集成了所有AOP(切面编程)功能。
- Spring Web：提供了基础的 Web 开发的上下⽂信息，现有的Web框架，如JSF、Tapestry、Structs 等，提供了集成。
- Spring Web MVC：提供了 Web 应⽤的 Model-View-Controller 全功能实现。



### 3. Spring中的bean的作用域有哪些？

* singleton：唯一bean实例，Spring中的bean默认都是单例的。
* prototype：每次请求都会创建一个新的bean实例。
* request：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
* session：每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP session内有效。
* global-session：全局session作用域，仅仅在基于Portlet的Web应用中才有意义，Spring5中已经没有了。



### 4. Spring是怎么解决循环依赖的？

整个IOC容器解决循环依赖，用到的几个重要成员

- **singletonObjects**:一级缓存，存放完全初始化好的Bean的集合，从这个集合中取出的Bean可以立马返回。
- **earlySingletonObjects**：二级缓存，存放创建好但没有初始化属性的Bean的集合，它用来解决循环依赖。
- **singletonFactories**：三级缓存，存放单实例Bean工厂的集合。
- **singletonsCurrentlyInCreation**：存放正在被创建的Bean的集合。

IOC容器解决循环依赖的思路：

1.  初始化Bean之前，将这个BeanName放入三级缓存
2.  将准备创建的Bean放入`singletonsCurrentlyInCreation`（正在创建的Bean）
3.  `createNewInstance()`方法执行完后执行 `addSingletonFactory()`，将这个实例化但没有属性赋值的 Bean放入二级缓存，并从三级缓存中移除
4.  属性赋值&自动注入时，引发关联创建
5.  关联创建时，检查**正在被创建的Bean**中是否有即将注入的Bean。如果有，检查二级缓存中是否有当前创建好但没有赋值初始化的Bean。如果没有，检查三级缓存中是否有正在创建中的Bean。 至此一般会有，将这个Bean放入二级缓存，并从三级缓存中移除。
6.  之后Bean被成功注入，最后执行`addSingleton()`，将这个完全创建好的Bean放入一级缓存，从二级缓存和三级缓存移除，并记录已经创建了的单实例Bean。



### 5. Spring中的bean生命周期了解吗？

* **实例化Bean**:  Ioc容器通过获取BeanDefinition对象中的信息进⾏实例化，实例化对象被包装在 BeanWrapper对象中。
* **设置对象属性（DI）**：通过BeanWrapper提供的设置属性的接⼝完成属性依赖注⼊
* **注⼊Aware接⼝**：Spring会检测该对象是否实现了xxxAware接⼝，并将相关的 xxxAware实例注⼊给bean，通过xxxAware实例可以获取到获取其它 Bean，常用：
  - 实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字
  - 实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader 对象的实例。
  - 实现了BeanFactoryAware接口，调用setBeanClassFacotory()方法，传入ClassLoader对象 的实例。
* **BeanPostProcessor**：⾃定义的处理（分前置处理`postProcessBeforeInitialization()`和后置处理`postProcessAfterInitialization()`，后置处理在bean初始化方法之后执行）
* **InitializingBean和init-method**：如果Bean实现了InitializingBean接口，执行afeterPropertiesSet()方法。.如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
* **使用bean**
* **destroy**：当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。

### 6. SpringMVC工作流程了解吗？

1. ⽤户请求发送给`DispatcherServlet`，`DispatcherServlet`调⽤`HandlerMapping`处理器映射器
2. `HandlerMapping`根据xml或注解找到对应的处理器，⽣成处理器对象返回给`DispatcherServlet`
3. `DispatcherServlet`会调⽤相应的`HandlerAdapter`
4. `HandlerAdapter`经过适配调⽤具体的处理器去处理请求，⽣成`ModelAndView`返回给`DispatcherServlet`
5. `DispatcherServlet`将`ModelAndView`传给`ViewReslover`解析⽣成`View`返回给`DispatcherServlet`
6. `DispatcherServlet`根据`View`进⾏渲染视图

请求->`DispatcherServlet->HandlerMapping->Handler ->DispatcherServlet->HandlerAdapter处理handler- >ModelAndView ->DispatcherServlet->ModelAndView->ViewReslover->View ->DispatcherServlet->` 返回给客户



### 7. Spring框架中用到了哪些设计模式？

- 工厂设计模式：Spring使用工厂模式通过`BeanFactory`和`ApplicationContext`创建bean对象。
- 代理模式：AOP功能的原理就使⽤代理模式（JDK动态代理 、CGLib字节码⽣成技术代理)。
- 单例设计模式：Spring中的bean默认都是单例的。
- 模板方法模式：Spring中的`jdbcTemplate`、`hibernateTemplate`等以Template结尾的对数据库操作的类，它们就使用到了模板模式。
- 包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问 不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源
- 观察者模式：Spring事件驱动模型就是观察者模式很经典的一个应用。
- 适配器模式：Spring AOP的增强或通知（Advice）使用到了适配器模式、Spring MVC中也是用到了适配器模式适配Controller。
- 策略模式： Bean的实例化的时候决定采⽤何种⽅式初始化bean实例(反射或者`CGLIB`动态字节码⽣成）



### 8. Spring中单例Bean线程安全问题

单例bean存在线程问题， 主要是因为当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。 有两种常见的解决方案：

 1.在bean对象中尽量避免定义可变的成员变量（不太现实）。

 2.在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中（推荐的一种方式）。



### 9. 谈谈对Spring中的AOP理解

传统oop开发代码逻辑⾃上⽽下的，这个过程中会产⽣⼀些横切性问题，这些问题与我们主业务逻辑关系不⼤，会散落在代码的各个地⽅，造成难以维护，aop思想就是把业务逻辑与横切的问题进⾏分离， 达到解耦的⽬的，提⾼代码重⽤性和开发效率。

 **应用场景**

* 记录日志
* 监控性能
* 权限控制
* 事务管理
* 线程池关闭

**核心概念：**

* 切⾯（aspect）：类是对物体特征的抽象，切⾯就是对横切关注点的抽象

* 横切关注点：对哪些⽅法进⾏拦截，拦截后怎么处理，这些关注点称之为横切关注点。

* 连接点（joinpoint）：被拦截到的点，因为 Spring 只⽀持⽅法类型的连接点，所以在Spring 中连接点指的就是被拦截到的⽅法，实际上连接点还可以是字段或者构造器。
* 切⼊点（pointcut）：对连接点进⾏拦截的定义。
* 通知（advice）：所谓通知指的是拦截到连接点后要执⾏的代码，通知分为前置、后置、异 常、最终、环绕通知五类。
* ⽬标对象：代理的⽬标对象。
* 织⼊（weave）：将切⾯应⽤到⽬标对象并导致代理对象创建的过程。
* 引⼊（introduction）：在不修改代码的前提下，引⼊可以在运⾏期为类动态地添加⽅法或字段。

**AOP流程**

* @EnableAspectJAutoProxy给容器（beanFactory）中注册⼀个 AnnotationAwareAspectJAutoProxyCreator对象
* AnnotationAwareAspectJAutoProxyCreator对⽬标对象进⾏代理对象的创建，对象内部，是封装 JDK和CGlib两个技术，实现动态代理对象创建的（创建代理对象过程中，会先创建⼀个代理⼯⼚， 获取到所有的增强器（通知⽅法），将这些增强器和⽬标类注⼊代理⼯⼚，再⽤代理⼯⼚创建对象）
* 代理对象执⾏⽬标⽅法，得到⽬标⽅法的拦截器链，利⽤拦截器的链式机制，依次进⼊每⼀个拦截器进⾏执⾏。

**动态代理与静态代理区别**

* 静态代理，程序运⾏前代理类的.class⽂件就存在了
* 动态代理：在程序运⾏时利⽤反射动态创建代理对象

**CGLIB与JDK动态代理区别**

* JDK动态代理必须提供接⼝才能使⽤。
* CGLIB只需要⼀个⾮抽象类就能实现动态代理

**AOP中的动态代理**

Spring AOP是基于动态代理的，如果要代理的对象实现了某个接口，那么Spring AOP就会使用JDK动态 代理去创建代理对象；

而对于没有实现接口的对象，就无法使用JDK动态代理，转而使用CGlib动态代理 生成一个被代理对象的子类来作为代理。

### 10. `@Component`和`@Bean`的区别是什么？

- 作用对象不同。`@Component`注解作用于类，而`@Bean`注解作用于方法。

- `@Component`注解通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用 @ComponentScan注解定义要扫描的路径）。`@Bean`注解通常是在标有该注解的方法中定义产生这个 bean，告诉Spring这是某个类的实例，当我需要用它的时候还给我。 
- `@Bean`注解比`@Component`注解的自定义性更强，而且很多地方只能通过@Bean注解来注册bean。 比如当引用第三方库的类需要装配到Spring容器的时候，就只能通过@Bean注解来实现。



### 11. Spring 事务管理的方式有几种？

1.**编程式事务**：在代码中硬编码（不推荐使用）。 

2.**声明式事务**：在配置文件中配置（推荐使用），分为基于XML的声明式事务和基于注解的声明式事务。



### 12. Spring事务的隔离级别有哪几种？

在TransactionDefinition接口中定义了五个表示隔离级别的常量：

- **ISOLATION_DEFAULT**：使用后端数据库默认的隔离级别，Mysql默认采用的REPEATABLE_READ隔离级别；Oracle默认采用的READ_COMMITTED隔离级别。 
- **ISOLATION_READ_UNCOMMITTED(读未提交)**：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
- **ISOLATION_READ_COMMITTED(读已提交)**：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生 
- **ISOLATION_REPEATABLE_READ(可重复读)**：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己 所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。 
- **ISOLATION_SERIALIZABLE(串行化)**：最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行， 这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这 将严重影响程序的性能。通常情况下也不会用到该级别。



### 13. Spring事务中有哪几种事务传播行为？

在TransactionDefinition接口中定义了八个表示事务传播行为的常量。

**支持当前事务的情况**：
-  **PROPAGATION_REQUIRED**：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。 
-  **PROPAGATION_SUPPORTS**： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。 
-  **PROPAGATION_MANDATORY**： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常（mandatory：强制性）。

**不支持当前事务的情况**：

- **PROPAGATION_REQUIRES_NEW**： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- **PROPAGATION_NOT_SUPPORTED**： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **PROPAGATION_NEVER**： 以非事务方式运行，如果当前存在事务，则抛出异常。

**其他情况**：

- **PROPAGATION_NESTED**： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`PROPAGATION_REQUIRED`。



  

  

  

  





​	



