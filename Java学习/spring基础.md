



# 一 Spring

### 1.1 背景


- ## Spring以interface21为基础

- Spring作者是Rod Johnson
- Spring理念,使现有的技术更加容易使用,整合现有的技术框架,大砸会

SSM:SpringMVC+Spring+Mybatis

下载:

https://repo.spring.io/libs-release-local/org/springframework/spring/

github:

https://github.com/spring-projects/spring-framework/issues



文档:

https://docs.spring.io/spring-framework/docs/3.0.x/javadoc-api/org/springframework/

### 1.2 优点

> spring是轻量级的控制反转和面向切片的框架

- spring是开源的免费的框架
- spring是一个轻量,非入侵式的框架
- 控制反转IOC,面向切片AOP
- 支持事务的处理
- 对框架整合的支持

### 1.3 组成

![image-20210115200434148](/home/kalao/.config/Typora/typora-user-images/image-20210115200434148.png)

### 1.4 扩展

![image-20210115200615489](/home/kalao/.config/Typora/typora-user-images/image-20210115200615489.png)

- springboot
  - 一个快速开发的脚手架
  - 基于springBoot可以快速的开发单个微服务
  - 约定大于配置
  - 基础spring+springmvc
- springcloud
  - springcloud是基于springboot

# 二 IOC理论推导



在以前我们可以定义一个接口,然后调用的时候new一个实现类

如果对于一个内部代码,需要修改内部的实现,那么这样对于使用者来说,这是一个灾难,牵一发而动全身.那么如果从外部动态的设置,那么内部代码不需要改变





换句话说其实创建实现的方式取决于需求方,而不是接收方,对于接收方只需要一个接口.

在之前由于需求方没有所谓的多种需求(更改实现),那么就无所谓谁来提供创建,但是一旦需求多了,对于接受方如果频繁修改代码这是不现实,假如有10000个人有10000个不同的需求,那么接收方难道为每个需求更改一次源码吗,这是不合理的.让使用我们代码的人自己来选择实现.

- 这种思想,从本质上解决了问题,我们程序员不用在去管理对象的创建了.系统的耦合性大大降低.

![image-20210115213343916](/home/kalao/.config/Typora/typora-user-images/image-20210115213343916.png)

![image-20210115213649173](/home/kalao/.config/Typora/typora-user-images/image-20210115213649173.png)





### bean.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="xiaokuang" class="com.spring02.pojo.user">
        <property name="name" value="kuanghao"/>
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

### 实例化容器


```java
        ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
        user xiaokuang = (user)context.getBean("xiaokuang");
        System.out.println(xiaokuang.getName());
```

简单示例:

我们可以在xml文件中替换userDao的实现

![image-20210115223228881](/home/kalao/.config/Typora/typora-user-images/image-20210115223228881.png)



# 三 IOC容器(spring 配置)

### 3.1 别名

```xml
<alias name="xiaokuang" alias="xiaol"/>
```

不仅如此name还可以定义别名


```xml
<bean id="xiaokuang" class="com.spring02.pojo.user" name="user1 user2;user3,user4">
    <property name="name" value="kuanghao"/>
</bean>
```

### 3.2 Bean的配置



```xml
<bean id="xiaokuang" class="com.spring02.pojo.user" name="user1 user2;user3,user4">
    <property name="name" value="kuanghao"/>
</bean>
```

### 3.3 import

> 由于实际开发过程中,有可能几个人同时开发,那么几个人都有独立的bean.xml文件,那么需要将几个人的文件统一,最后统一使用总的(applicationContext.xml).

```xml
<import resource="bean1.xml"/>
```

### 3.4 Dependencies

> 依赖注入: Set注入
>
> 依赖: bean对象的创建依赖于容器
>
> 注入:bean对象中的所有属性,由容器来注入

#### 	3.4.1 构造器注入

#### 	3.4.2 set方式注入

- 普通值的注入

```xml
 <property name="name" value="kuanghao"/>
```

- bean注入,引用

```xml
 <property name="name" ref="kuanghao"/>
```

- 其他的

1. array,list

   ```xml
   <array>
   	<value>1</value>
   	<value>2</value>
   </array>
   <list>
   	<value>1</value>
   	<value>2</value>
   </list>
   ```

   

2. Map

   ```xml
   <map>
               <entry key="an entry" value="just some string"/>
               <entry key ="a ref" value-ref="myDataSource"/>
   </map>
   ```

   

3. set

```xml
<set>
            <value>just some string</value>
            <ref bean="myDataSource" />
</set>
```

- null

```xml
<null/>
```

- properties

```xml
    <property name="adminEmails">
        <props>
            <prop key="administrator">[emailprotected]</prop>
            <prop key="support">[emailprotected]</prop>
            <prop key="development">[emailprotected]</prop>
        </props>
    </property>
```



#### 3.4.3 扩展方式注入

c命名空间注入

> 允许你用bean元素的属性来代替嵌套的元素



p命名空间注入

> p-namespace 允许您使用`bean`元素的属性(而不是嵌套的`<property/>`元素)来描述协作 Bean 的属性值，或同时使用这两者。

```xml
<bean id="xiaokuang" class="com.spring02.pojo.user" name="user1 user2;user3,user4" p:status="1">
    <property name="name" value="kuanghao" />
    <!-- collaborators and configuration for this bean go here -->
</bean>
```

### 3.5 bean的作用域



| Scope                                                        | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [singleton](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-singleton) | (默认)将每个 Spring IoC 容器的单个 bean 定义范围限定为单个对象实例。 |
| [prototype](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-prototype) | 将单个 bean 定义的作用域限定为任意数量的对象实例。           |
| [request](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-request) | 将单个 bean 定义的范围限定为单个 HTTP 请求的生命周期。也就是说，每个 HTTP 请求都有一个在单个 bean 定义后面创建的 bean 实例。仅在可感知网络的 Spring `ApplicationContext`中有效。 |
| [session](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-session) | 将单个 bean 定义的范围限定为 HTTP `Session`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |
| [application](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/core.html#beans-factory-scopes-application) | 将单个 bean 定义的范围限定为`ServletContext`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |
| [websocket](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/web.html#websocket-stomp-websocket-scope) | 将单个 bean 定义的范围限定为`WebSocket`的生命周期。仅在可感知网络的 Spring `ApplicationContext`上下文中有效。 |

注意: 单例模式是默认的

### 3.6 bean的自动装配

- 自动装配是spring满足bean依赖一种方式
- spring会在上下文中自动寻找,并自动给bean装配属性

在spring中有三种装配的方式

1. 在xml中显式的配置
2. 在java中显式配置
3. 隐式的自动装配bean(重要)

- **在xml中显式的配置**

在bean的属性添加autowire="byName"

> 会自动在容器上下文中查找,和自己对象set方法后面的值对应的beanid,但是需要保证bean的id唯一

在bean的属性添加autowire="byType"

> 会自动在容器上下文中查找,和自己对象属性类型相同的bean,但是需要保证bean的class唯一

- 使用注解方式

spring2.5开始支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>	
```



1. 导入约束

   ```xml
   xmlns:context="http://www.springframework.org/schema/context"
   ```

   

2. 配置注解支持(不加,注解无效)

```xml
 <context:annotation-config/>
```

示例:

```xml
<bean id="category" class="com.spring02.pojo.category" p:name="good">
</bean>
```



```java
public class user {
    private String name;
    private Integer status;
    @Autowired
    private category category;
}
```

@Autowired

1. 可以直接在属性上使用,也可以在set方法上使用

2. 使用Autowired,我们可以不用编写set方法,前提是你这个自动装配的属性在IOC容器中存在,且符合名字byName

3. 科普

   字段标记了这个注解,说明这个字段可以为null

   ```java
   @Nullable 
   ```

   ```java
   public @interface Autowired {
       boolean required() default true;
   }//为false的时候字段允许为null
   ```

   @Qualifier(value="name")

   ```java
   public class user {
       private String name;
       private Integer status;
       @Autowired
       @Qualifier("category1")
       private category category;
   }
   ```

   @Resource相比@Autowired的区别
   
   - 都是用于自动装配
   - @Autowired是通过byType,而@Resource通过byName找不到,再通过byType
   - @Resource可以实现指定的beanid

### 3.7 使用注解开发

#### 1. 在spring4以后,要使用注解开发,必须保证aop的包导入了

![image-20210119142814764](/home/kalao/.config/Typora/typora-user-images/image-20210119142814764.png)

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.3.3</version>
</dependency>
```

#### 2. 指定要扫描的包

```xml
<context:component-scan base-package="com.spring02.pojo"/>
```

#### 3. 常用的一些注解

##### 1.@Component

> 名字默认是class的小写,@Component相当于如下写法:

```xml

<bean id="category" class="com.spring02.pojo.category"/>
```

```java
@Component
public class Category {
    private String name;
}

```



##### 		2.@Value

> 为属性注入值,相当于如下:,还可以在set方法上写

```xml
<property name="name" value="小伙子" />
```

```java
@Component
public class category {
    @Value("小伙子")
    private String name;
}
```

##### 		3.衍生的注解

@Component有几个衍生注解,我们在web开发中,会按照mvc三层架构分层

- dao         @Repository
- service   @Service
- controller @Controller

这四个功能是一样的,将类注册到spring

##### 4. 设置作用域 

@Scope("singleton")

#### 3.8 使用java的方式配置spring

![image-20210119175033066](/home/kalao/.config/Typora/typora-user-images/image-20210119175033066.png)







```java
@Configuration
@ComponentScan("com.spring02.pojo")
public class myConfig {
    @Bean
    public user getUser(){
        return new user();
    }
}
```

> 这个方法的名字,就相当于bean标签的id属性
>
> 这个方法的返回值,就相当于bean标签中的class属性
>
>  return new user(); 就是返回要注入到bean的对象



```java
@Component
public class user {
    private String name;
    private Integer status;
    @Resource
    private category category;
}
```

```java
@Component
public class category {
    @Value("小伙子")
    private String name;
}
```

@import 相当于导入其他的bean.xml

### 4.总结

注解相对简单,但是有局限,不是自己类使用不了.

xml更加万能,适用任何场合.

综合来看,注解用于属性注入,xml用于bean的管理

# 四 代理模式



![image-20210120161156924](/home/kalao/.config/Typora/typora-user-images/image-20210120161156924.png)

> 个人感觉,代理是种强化后的角色
>
> 在不改变原有角色的情况下,需要增强角色,代理模式能发挥其作用

## 4.1 角色分析

- 抽象角色: 一般会使用接口或者抽象类来解决
- 真实角色:被代理的角色
- 代理角色:代理真实角色的角色,代理真实角色后,我们一般会做一些附属操作
- 客户:访问代理角色的人

## 4.2代理模式的好处和坏处

好处:

- 可以使得真实角色的操作更加纯粹!不用去关注一些公共的业务(实现业务分工)
- 公共业务发生扩展的时候,方便集中管理

坏处:

- 一个代理对应一个真实角色,代码量增大,开发效率变低.



![image-20210120183922373](/home/kalao/.config/Typora/typora-user-images/image-20210120183922373.png)



## 4.3静态代理

示例:

```java
public interface userDao {
    void show();
}
```

```java
@Component
//@Getter
public class userDaoImpl implements  userDao {
    @Override
    public void show() {
        System.out.println("this is userDaoImpl");
    }
}
```

```java
@Component
public class userDaoProxy implements userDao {
    @Autowired
    private userDao userDao;
    @Override
    public void show() {
        System.out.println("start...");
        userDao.show();
        System.out.println("end...");
    }
}
```

## 4.4动态代理



> 动态代理是动态生成的,动态代理分为两类,一类基于接口_(JDK动态代理),一种基于类_(cglib),还有基于java字节码_(javasisit)

![image-20210120192647475](/home/kalao/.config/Typora/typora-user-images/image-20210120192647475.png)



```java
@AllArgsConstructor
public class ProxyInvocationHandler implements InvocationHandler {
    private Object ud;
    //动态生成代理类,需要传递要代理对象实现的接口,即可动态生成,相比之前自己创建来的简便
    public  Proxy getProxy(){
        return (Proxy) Proxy.newProxyInstance(this.getClass().getClassLoader()
                ,ud.getClass().getInterfaces()
                , this);
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("hi----------");
        method.invoke(ud,args);
        return null;
    }
}
```

# 五 AOP



涉及的场景

**日志,安全,缓存**,**事务**



![image-20210120205009450](/home/kalao/.config/Typora/typora-user-images/image-20210120205009450.png)



![image-20210120205133779](/home/kalao/.config/Typora/typora-user-images/image-20210120205133779.png)



![image-20210120205518096](/home/kalao/.config/Typora/typora-user-images/image-20210120205518096.png)

## 5.2 spring接口实现



在org.springframework.aop下的一堆接口

![image-20210120210302544](/home/kalao/.config/Typora/typora-user-images/image-20210120210302544.png)

需要引入依赖

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.2</version>
</dependency>
```

### MethodBeforeAdvice接口

> 用于前置通知

![image-20210120214246123](/home/kalao/.config/Typora/typora-user-images/image-20210120214246123.png)

```java
@Component
public class log implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println("before");

    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:component-scan base-package="com.spring02.dao"/>
    <context:annotation-config/>
    <aop:config>
        //切入点  expression表示要切入的位置
        <aop:pointcut id="pointcut" expression="execution(* com.spring02.dao.userDaoImpl.*(..))"/>
        //通知
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    </aop:config >

</beans>
```

```java
@Component
//@Getter
public class userDaoImpl implements  userDao {
    @Override
    public void show() {
        System.out.println("this is userDaoImpl");
    }
}
```

注意:

```java
userDao userDaoImpl = (userDao)context.getBean("userDaoImpl");//测试的时候,这里不能转为userDaoImpl,由于此时已经为代理对象,所以只能向上转型,而不能同级转型
userDaoImpl.show();
```

#### 5.2.1 expression表达式

## 5.3 自定义类实现

> 不需要像上述方法,每次为每个通知都实现一个类,而在一个自定义类统一解决

beans.xml

```xml
<aop:config>
    <!-- 定义切面-->
    <aop:aspect ref="newLog">
        <!--定义切入点-->
        <aop:pointcut id="pointcut" expression="execution(* com.spring02.dao.userDaoImpl.*(..))"/>
        <!--定义通知-->
        <aop:before method="before" pointcut-ref="pointcut"/>
        <aop:after method="after" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```

```java
@Component
public class newLog {
    public void before(){
        System.out.println("before");
    }
    public void after(){
        System.out.println("after");

    }
}
```

## 5.4 注解的方式

###   1. 首先开启aop注解支持

```xml
<!-- 扫描要检查注解的包-->
    <context:component-scan base-package="com.spring02.dao"/>
<!-- 开启spring注解支持   -->
    <context:annotation-config/>
<!--  开启aop注解支持 JDK(默认)和cglib-->
    <aop:aspectj-autoproxy/>
```

```xml
<!--jdk-->
<aop:aspectj-autoproxy proxy-target-class="false"/>
<!-- cglib-->
<aop:aspectj-autoproxy proxy-target-class="true"/>
```


```java
@Component
@Aspect //切面
public class newLog {
    @Before("execution(* com.spring02.dao.userDaoImpl.*(..))")//切入点,通知
    public void before(){
        System.out.println("before");
    }
    @After("execution(* com.spring02.dao.userDaoImpl.*(..))")
    public void after(){
        System.out.println("after");

    }
}
```

```
before
this is userDaoImpl
after
```

### 2. 环绕



```java
@Around("execution(* com.spring02.dao.userDaoImpl.*(..))")
public void around(ProceedingJoinPoint jp){
    System.out.println("环绕前");
    try {
        jp.proceed();
    } catch (Throwable throwable) {
        throwable.printStackTrace();
    }
    System.out.println("环绕后");
}
```

```java
output:
    环绕前
    before
    this is userDaoImpl
    after
    环绕后
```





# 一些疑问



1. 

   我遇到了一个例子`@Autowired`：

   ```
   public class EmpManager {
      @Autowired
      private EmpDao empDao;
   }
   ```

   我很好奇`empDao`如何注入的，因为没有setter方法，而且它是私有的。

> Java allows access controls on a field or method to be turned off 
>
> 如上所说,java允许权限控制被关闭,那么私有的也可以访问,因而可以被注入

- http://stackoverflow.com/questions/3536674/how-does-spring-autowired-work
- http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/util/ReflectionUtils.html

# 六 整合mybatis

## 1. 步骤

####  1.导入相关jar包

- junit
- mysql数据库
- mybatis
- spring相关
- mybatis-spring 整合包
- aop注入

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.23</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.3</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.6</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.2</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.9.RELEASE</version>
</dependency>
```

#### 2. bean.xml文件

这里已经将mybatis-config文件融入到这个bean.xml文件里,并将sqlSessionFactory,sqlSession也交由spring来管理

这里是mybatis-spring这个包下提供的SqlSessionFactoryBean和SqlSessionTemplate(注意只能构造器注入)来实例化.

另外自己YY一下spring-jdbc这个替换也隐含了IOC的原理,我想替换只要写个bean就行.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
        <!--使用的是spring-jdbc来代替mybatis里的jdbc-->
        <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
            <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
            <property name="username" value="root"/>
            <property name="password" value="A123456a&amp;"/>
        </bean>
        <!--sqlSessionFactory-->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <!--就是mybatis.xml配置的那些内容 -->
            <property name="dataSource" ref="datasource"/>
             <!--这里还可以绑定mybatis-config ,由于我全部放在bean.xml中,所以感觉不太需要-->
             <property name="configLocation" value=""/>
            <property name="mapperLocations" value="classpath:com/sm/dao/userMapper.xml"/>
        </bean>
        <!-- sqlSession-->
        <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
            <!--只能通过构造器的方法注入-->
            <constructor-arg index="0" ref="sqlSessionFactory"/>
        </bean>
        <!--service-->
        <bean id="userServiceImpl" class="com.sm.service.userServiceImpl">
            <property name="sqlSession" ref="sqlSession"/>
        </bean>
</beans>
```



```java
public interface userMapper {
//    @Select("select * from user")
    public List<user> findAllUser();
//    @Select("select * from user where id=#{id}")
    public user findById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sm.dao.userMapper">
    <select id="findAllUser" resultType="com.sm.pojo.user">
        select * from user
  </select>
</mapper>
```

```java
@Setter
public class userServiceImpl implements userServcie {
    private SqlSessionTemplate sqlSession;

    public void findAllUser(){
        System.out.println("这是service...");
        userMapper mapper = sqlSession.getMapper(userMapper.class);
        List<user> allUser = mapper.findAllUser();
        for (user user: allUser) {
            System.out.println(user);
        }
    }

}
```

#### 3.测试

```java
public class userServiceImplTest {
    public static ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");

    @Test
    public void findAllUser() {
        userServcie userServiceImpl = (userServcie) context.getBean("userServiceImpl");
        userServiceImpl.findAllUser();
    }
}
```

```
这是service...
user(id=1, name=1)
user(id=2, name=2)
user(id=3, name=3)
user(id=4, name=4)
user(id=5, name=5)
```

#### 4. SqlSessionDaoSupport是一个抽象的支持类

> 一旦继承之后,它会帮我们生成sqlSession,在spring管理的时候,也就少一个sqlsession的注入,取而代之的是对继承的这个对象需要注入sqlsessionFactory.



# 七 声明式事务



1. 一组业务要么成功,要么失败
2. 事务在项目开发中,十分重要,涉及到数据的完整性和一致性

事务的ACID原则

- 原子性
- 一致性
- 隔离性

多个业务可能操作同一个资源,防止数据损坏

- 持久性

事务一旦提交,无论系统发生什么问题,结果都不会被影响

## spring的事务管理

- ### 声明式 如下操作,可以不需要修改源码,在配置文件里面使用aop方法实现

- 编程式  在代码里面显式的写异常处理,然后进行回滚

![image-20210121173329966](/home/kalao/.config/Typora/typora-user-images/image-20210121173329966.png)

![image-20210121173533570](/home/kalao/.config/Typora/typora-user-images/image-20210121173533570.png)




```xml
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="datasource"/>
  </bean>
<tx:advice id="txAdvice" transaction-manager="transactionManager">
      <tx:attributes>
          <tx:method name="add" propagation="REQUIRED"/>
          <tx:method name="delete" propagation="REQUIRED"/>
          <tx:method name="query" read-only="true"/>
          <tx:method name="update" propagation="REQUIRED"/>
          <tx:method name="findAllUser" propagation="REQUIRED"/>
      </tx:attributes>
</tx:advice>



<aop:config>
    <aop:pointcut id="txpointcut" expression="execution(* com.sm.*.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txpointcut"/>
</aop:config>
```



```xml
<tx:method name="add" propagation="REQUIRED"/>
          <tx:method name="delete" propagation="REQUIRED"/>
          <tx:method name="query" read-only="true"/>
          <tx:method name="update" propagation="REQUIRED"/>
```

上述其实是对表的业务做了事务操作

```xml
 <tx:method name="findAllUser" propagation="REQUIRED"/>
```

上述是对一个具体的业务(service层的findAlluser)做了事务操作



```java
public void findAllUser(){
    System.out.println("这是service...");
    userMapper mapper = sqlSession.getMapper(userMapper.class);
    mapper.addUser(new user(5,"5"));
    mapper.delUser(new user(5,"5"));

}
```

```xml
<insert id="addUser" parameterType="com.sm.pojo.user">
    insert into user(id,name) values (#{id},#{name})
</insert>
<delete id="delUser" parameterType="com.sm.pojo.user">
    deletes from user where id =#{id}
</delete>
```

上面的deletes是故意写错,在不加事务管理的时候,    mapper.addUser(new user(5,"5"));是可以执行成功,但是 mapper.delUser(new user(5,"5"));出现异常.

在加了事务处理,要么都成功,要么只要一方失败,都会失败