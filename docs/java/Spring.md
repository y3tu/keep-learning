[TOC]

#### Spring框架的七⼤模块

Spring Core：框架的最基础部分，提供 IoC 容器，对 bean 进⾏管理。

Spring Context：继承BeanFactory，提供上下⽂信息，扩展出JNDI、EJB、电⼦邮件、国际化等功能。

Spring DAO：提供了JDBC的抽象层，还提供了声明性事务管理⽅法。

Spring ORM：提供了JPA、JDO、Hibernate、MyBatis 等ORM映射层。

Spring AOP：集成了所有AOP(切面编程)功能。

Spring Web：提供了基础的 Web 开发的上下⽂信息，现有的Web框架，如JSF、Tapestry、Structs 等，提供了集成。

Spring Web MVC：提供了 Web 应⽤的 Model-View-Controller 全功能实现。



**IOC(控制反转)**：将对象的创建权，由Spring管理。

**DI(依赖注⼊)**：在Spring创建对象的过程中，把对象依赖的属性注⼊到类中。



#### Bean定义5种作⽤域

* **singleton（单例）**是指在Spring IoC容器中仅存在一个Bean的示例，Bean以单实例的方式存在，**单实例模式**是重要的设计模式之一，在Spring中对此实现了超越，可以对那些**非线程安全的对象**采用单实例模式。在默认的情况下Spring的ApplicationContext容器在启动的时候，自动实例化所有的singleton的bean并缓存于容器当中。

  虽然启动时会花费一定的时间，但是他却带来了两个**好处**:

  1. 对bean提前的实例化操作，会及早发现一些潜在的配置的问题。
  2. Bean以缓存的方式运行，当运行到需要使用该bean的时候，就不需要再去实例化了。加快了运行效率。

* **prototype（原型）**每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，相当于执行new Bean()的操作。在默认情况下，**Spring容器在启动时不实例化prototype的Bean。**此外，Spring容器将prototype的实例交给调用者之后便不在管理他的生命周期了。

* **request** 对应一个http请求和生命周期，当http请求调用作用域为request的bean的时候,Spring便会创建一个新的bean，在请求处理完成之后便及时销毁这个bean。

* **session** Session中所有http请求共享同一个请求的bean实例。Session结束后就销毁bean。

* **globalSession** 与session大体相同，但仅在**portlet**应用中使用。

* **Portlet规范定义了全局session的概念。**请求的bean被组成所有portlet的自portlet所共享。如果不是在portlet这种应用下，**globalSession则等价于session作用域**

**自定义作用域：**Spring的Bean作用域机制是可以扩展的，这意味着，你不仅可以使用Spring提供的预定义Bean作用域，还可以定义自己的作用域，甚至重新定义现有的作用域（不提倡这么做，而且你不能覆盖内置的singleton和prototype作用域）

1.实现自定义Scope类

`org.springframework.beans.factory.config.Scope`

 首先我们得实现Scope这个接口，要将自定义scope集成到Spring容器当中就必须要实现这个接口。接口中只有两个方法，分别用于底层存储机制获取和删除这个对象。

2.在实现一个或多个自定义Scope并测试通过之后，接下来便是如何让Spring容器来识别新的作用域。我们需要注册自定义Scope类，使用registerScope方法

   `ConfigurableBeanFactory.registerScope(String scopeName, Scope scope)`

其中：第一个参数是与作用域相关的全局唯一的名称，第二个参数是准备实现的作用域的实例。

3.在实现和注册自定义的scope类之后我们便来使用自定义的Scope:

```java
Scope customScope = new ThreadScope();  
beanFactory.registerScope(“thread”, customeScope);  

<bean id=“***” class=“***”  scope=“thread” />  
```



#### Spring IOC初始化流程

**1.Resource定位**:即寻找⽤户定义的bean资源，由ResourceLoader通过统⼀的接⼝Resource接⼝来完成。

**2.BeanDefinition的载入**：BeanDefinitionReader读取、解析Resource定位的资源成BeanDefinition载⼊到IOC中（通过HashMap进⾏维护BeanDefinition）。

**3.BeanDefinition注册**：即向IOC容器注册这些BeanDefinition， 通过 BeanDefinitionRegistery实现。



####  DI依赖注⼊流程

流程在Ioc初始化后，依赖注⼊的过程是⽤户第⼀次向IoC容器索要Bean时触发。

* 如果设置lazy-init=true，会在第⼀次getBean的时候才初始化bean， lazy-init=false，会容器启动的时候直接初始化（singleton bean）
* 调⽤BeanFactory.getBean()⽣成bean的
* ⽣成bean过程运⽤装饰器模式产⽣的bean都是beanWrapper（bean的增强）

**依赖注⼊怎么处理bean之间的依赖关系?**

其实就是通过在beanDefinition载⼊时，如果bean有依赖关系，通过占位符来代替，在调⽤getBean()时候，如果遇到占位符，从ioc⾥获取bean注⼊到本实例来。



#### Bean生命周期

* **实例化Bean**:  Ioc容器通过获取BeanDefinition对象中的信息进⾏实例化，实例化对象被包装在 BeanWrapper对象中。
* **设置对象属性（DI）**：通过BeanWrapper提供的设置属性的接⼝完成属性依赖注⼊
* **注⼊Aware接⼝**：Spring会检测该对象是否实现了xxxAware接⼝，并将相关的 xxxAware实例注⼊给bean，通过xxxAware实例可以获取到获取其它 Bean
* **BeanPostProcessor**：⾃定义的处理（分前置处理和后置处理）
* **InitializingBean和init-method**：执⾏自定义的初始化⽅法
* **使用bean**
* **destroy**：bean的销毁



#### Spring的IOC注⼊⽅式

* 构造器注⼊
*  setter⽅法注⼊ 
* 注解注⼊ 
* 接⼝注入



#### Spring中的循环依赖

**1.检测是否存在循环依赖**

Bean在创建的时候可以给该Bean打标，如果递归调⽤回来发现正在创建中的话，即说明了循环依赖了。

**2.Spring中循环依赖场景**

* 构造器的循环依赖
* 属性的循环依赖



#### Spring中使用的设计模式

* 工厂模式：Spring中的BeanFactory就是简单⼯⼚模式的体现，根据传⼊唯⼀的标识来获得bean对象。
* 单例模式：提供了全局的访问点BeanFactory。
* 代理模式：AOP功能的原理就使⽤代理模式（JDK动态代理 、CGLib字节码⽣成技术代理)。
* 装饰器模式： 依赖注⼊就需要使⽤BeanWrapper。
* 观察者模式：Spring中Observer模式常⽤的地⽅是listener的实现，如ApplicationListener。
* 策略模式： Bean的实例化的时候决定采⽤何种⽅式初始化bean实例(反射或者CGLIB动态字节码⽣成）



#### Spring中的AOP

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

当bean的实现中存在接⼝或者是Proxy的⼦类使用jdk动态代理；如果不存在接⼝，则采⽤ CGLIB来⽣成代理对象。



#### SpringMVC流程

1. ⽤户请求发送给DispatcherServlet，DispatcherServlet调⽤HandlerMapping处理器映射器
2. HandlerMapping根据xml或注解找到对应的处理器，⽣成处理器对象返回给DispatcherServlet
3. DispatcherServlet会调⽤相应的HandlerAdapter
4. HandlerAdapter经过适配调⽤具体的处理器去处理请求，⽣成ModelAndView返回给 DispatcherServlet 
5. DispatcherServlet将ModelAndView传给ViewReslover解析⽣成View返回给DispatcherServlet
6. DispatcherServlet根据View进⾏渲染视图

请求->DispatcherServlet->HandlerMapping->Handler ->DispatcherServlet->HandlerAdapter处理handler- >ModelAndView ->DispatcherServlet->ModelAndView->ViewReslover->View ->DispatcherServlet-> 返回给客户

​	



