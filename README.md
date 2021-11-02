# Spring

### 1.Spring简介

- spring:春天，给软件行业带来了春天
- 理念：使得现有技术更加实用
- 2002, 首次推出了Spring框架的雏形: interface21框架!
- Spring框架即以interface21框架为基础，经过重新设计 ，并不断丰富其内涵,于2004年3月24日发布了1.0正式版。
- Rod Johnson , Spring Framework创始人, 著名作者。很难想象Rod Johnson的学历， 真的让好多人大吃一惊，他是悉尼大学的博士,然而他的专业不是计算机，而是音乐学。
- spring理念: 使现有的技术更加容易使用，本身是一 个大杂烩，整合了现有的技术框架!
- SSH : Struct2 + Spring + Hibernate!
- SSM : SpringMvc + Spring + Mybatis!
- 官网: https://spring.io/projects/spring-framewor
- 官方下载地址: http://repo.spring.io/release/org/springframework/spring
- GitHub: https://github.com/spring-projects/spring-framework

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.3</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.3</version>
</dependency>
```

#### 优点

1. Spring是一个开源的免费的框架（容器）！
2. Spring是一个轻量级、非入侵式的框架！
3. 控制反转（IOC），面向切面编程（AOP）
4. 支持事务的处理，对框架整合的支持！

#### 缺点

1. 配置十分繁琐，人称“配置地狱”！

#### **总结**

**Spring就是一个轻量级的控制反转（IOC）和面向切面编程(AOP)的框架！**

#### 组成

![image-20210203150357582](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@7be4b1577cf05dcb7d09e96e8b9141b9120996b9/2021/03/03/32801aac3fd4e89571d1615acf9339c3.png)

#### 拓展

现代化的Java开发：

![image-20210203150510001](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@bb798397dbc3c2678330381ebd060f532c6f8d9d/2021/03/03/58a02f0675213af70f59cfc7fdaa1bb4.png)

- Spring Boot
  1. 一个快速开发的脚手架
  2. 基于SpringBoot可以快速开发单个微服务
  3. 约定大于配置
- Spring Cloud
  1. SpringCloud是基于SpringBoot实现的

**学习Spring Boot的前提，需要完全掌握Spring及Spring MVC!承上启下的作用！**

### 2.IoC（控制反转！！！）

#### 1.IoC理论推导

- UserDao接口
- UserDaoImpl实现类
- UserService业务接口
- UserServiceImpl业务实现类

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改原代码！如果程序代码量十分大，修改一次的成本十分昂贵！

我们使用一个set接口实现：

```java
private UserDao userDao;

//利用set动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
public void getUser() {
    userDao.getUser();
}
```

- 之前，程序是主动创建对象！控制权在程序手上！
- 使用了Set注入后，程序不再具有主动性，而是变成了被动的接受对象！

这种思想，从本质上解决了问题，我们程序员不用再去管理对象的创建了。系统的耦合性大大降低，可以更加专注在业务的实现上。

#### 2.IoC本质

IoC(Inversion of Control),是一种种设计思想, DI(Dependency Injection依赖注入)是实现IoC的一种方法，也有人认为DI只是loC的另一种说法。没有loC的程序中,我们使用面向对象编程,对象的创建与对象间的依赖关系完全硬编码在程序
中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方,个人认为所谓控制反转就是:获得依赖
对象的方式反转了。

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者融为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到0配置的目的。

控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方式是依赖注入（DI）

### 3.编写第一个Spring程序HelloSpring!

#### 1.首先定义一个实体类

```java
package com.luo.pojo;

public class Hello {
    private String str;

    public void setStr(String str) {
        this.str = str;
    }

    public String getStr() {
        return str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

#### 2.然后配置Spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
<!--    使用Spring来创建对象，在Spring这些都称为Bean-->
    <bean id="hello" class="com.luo.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

</beans>
```

#### 3.编写测试类

```java
import com.luo.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        //获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在Spring中的管理了，我们要使用，直接去里面取用就可以了
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```

#### 4.总结一下

***控制:****谁来控制对象的创建,传统应用程序的对象是由程序本身控制创建的,使用Spring后,对象是由Spring来创建的。*

***反转:***程序本身不创建对象,而变成被动的接收对象。

***依赖注入:***就是利用set方法来进行注入的。

IOC是一-种编程思想 ,由主动的编程变成被动的接收，可以通过new ClassPathXmlApplicationContext去浏览一下底层源码 。OK ,到了现在,我们彻底不用再程序中去改动了,要实现不同的操作,只需要在xml配置文件中进行修改。

**所谓的IoC,一句话搞定 :对象由Spring 来创建，管理,装配 !** 

### 4.IoC创建对象的方式

```xml
<!--        默认无参-->
         <bean id="user" class="com.luo.pojo.User">
            <property name="name" value="小明"/>
        </bean>
<!--    有参构造，下标赋值-->
        <bean id="user1" class="com.luo.pojo.User">
            <constructor-arg value="小红" index="0"/>
        </bean>
<!--    有参构造，类型赋值,不建议使用-->
        <bean id="user2" class="com.luo.pojo.User">
            <constructor-arg type="java.lang.String" value="小亮"/>
        </bean>
<!--    直接通过参数赋值-->
    <bean id="user3" class="com.luo.pojo.User">
        <constructor-arg name="name" value="小小"/>
    </bean>
```

**总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！**

### 5.Spring配置

#### 1.别名

```xml
<!--    别名-->
    <alias name="user" alias="0"/>
    <alias name="user1" alias="1"/>
```

#### 2.Bean的配置

这里也可以为其取别名，而且更加灵活

```xml
<bean id="user3" class="com.luo.pojo.User" name="q w e r t y">
    <constructor-arg name="name" value="小小"/>
</bean>
```

#### 3.import

创建一个applicationContext.xml，一般用来**团队协作开发**，导入配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?><beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd">    <import resource="beans.xml"/></beans>
```

定义上下文的时候直接调用这个配置，同样可以实现

### 6.依赖注入

- 构造器注入
- set方式注入（重点）
- 拓展方式注入：cp

```xml
<bean id="s" class="com.luo.pojo.Address">        <property name="address" value="HeNan"/></bean> <bean name="student" class="com.luo.pojo.Student"><!--        第一种，普通注入,value-->        <property name="name" value="test"/><!--        第二种，Bean注入,ref-->        <property name="address" ref="s"/><!--        数组注入-->        <property name="books">           <array>               <value>三国演义</value>               <value>水浒传</value>               <value>红楼梦</value>               <value>西游记</value>           </array>        </property><!--        List注入-->        <property name="hobbys">                <list>                    <value>听歌</value>                    <value>打游戏</value>                    <value>碎觉</value>                </list>        </property><!--        Map注入-->        <property name="card">            <map>                <entry key="身份证" value="11112222"/>                <entry key="银行卡" value="2131"/>                <entry key="学号" value="2132131"/>            </map>        </property><!--        Set注入-->        <property name="games">            <set>                <value>LOL</value>                <value>COC</value>                <value>BOB</value>            </set>        </property><!--        null-->        <property name="wife">            <null/>        </property><!--        Properties-->        <property name="info">            <props>                <prop key="学号">20191574308</prop>                <prop key="性别">男</prop>                <prop key="姓名">小落</prop>            </props>        </property>    </bean>
```

```xml
<?xml version="1.0" encoding="UTF-8"?><!--导入p命名空间约束与c命名空间约束--><beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xmlns:p="http://www.springframework.org/schema/p"       xmlns:c="http://www.springframework.org/schema/c"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd">    <!--    p命名空间注入，可以直接注入属性的值：property-->    <bean id="user" class="com.luo.pojo.User" p:age="18" p:name="小明" />    <bean id="user2" class="com.luo.pojo.User" c:age="18" c:name="熊熊"/></beans>
```

- Bean的作用域

  ![image-20210205094549722](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@18cc2048d0e4dcf920de015f6a38a0c4f4a458a8/2021/03/03/4617bd802fca0b2876aea208746026d5.png)

  1. 单例模式（Spring默认机制）

  ![image-20210205094521093](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@3812c334c39e102f23ddec706b3d6c77a3abb90f/2021/03/03/c1fdccd5428dd5d8042c890186d72735.png)

  2. 原型模式：**每次从容器中get的时候，都会产生一个新对象**！

  ```xml
  <bean id="user2" class="com.luo.pojo.User" c:age="18" c:name="熊熊" scope="prototype"/>
  ```

  3. 其余的request、session、application，这些只能在web开发中使用！

### 7.Bean的自动装配

- 自动装配是Spring满足Bean依赖的一种方式！
- Spring会在上下文中自动寻找，并自动给Bean装配属性！

在Spring中有三种装配的方式

1. 在xml中显式的配置
2. 在Java中显式的配置
3. **隐式的自动配置bean[重要]**

 

```xml
    <bean id="cat" class="com.luo.pojo.Cat"/>    <bean id="dog" class="com.luo.pojo.Dog"/><!--    会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean的id！-->    <bean id="people" class="com.luo.pojo.People" p:name="小落" autowire="byName"/><!--    会自动在容器上下文中查找，和自己对象属性类型相同的bean -->    <bean id="people" class="com.luo.pojo.People" p:name="小落" autowire="byType"/>
```

4. 使用注解实现自动装配 

   jdk1.5支持的注解，Spring2.5就支持注解了！
   基于注释的配置的引入提出了这样的方法是否“更好”的问题，而不是XML。

   要使用注解须知道：

   1. 导入约束,context约束
   2. 配置注解的支持

   ```xml
   <?xm1 version="1. 0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/ schema/beans"xmlns:xsi ="http://www.w3.org/2001/XMLSchema-instance"xmlns:context="http://www.springframework.org/schema/context"xsi:schemalocation="http://www.springframework.org/schema/beanshttps://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/contexthttps://www.springframework.org/schema/context/spring-context.xsd"><context:annotation-config/></beans>
   ```

   @Autowired

   直接在属性上使用即可！也可以在set方式上使用！

   使用Autowired，我们可以不用编写Set方法了，前提是自己自动装配的属性在IoC（Spring）容器中存在，且符合名字byName!

   科普一下：

   - @Nullable 说明这个字段可以为null

     ```java
       public People(@Nullable Dog dog) {        this.dog = dog;    }
     ```

   - 两种装配模式：

     ```java
     //   自动装配的，如果显示的定义了Autowried属性为false,说明这个对象可以为空，否则不允许为空    @Autowired(required = false)    private Cat cat;    @Autowired    private Dog dog;
     ```

    如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候，我们可以使用@Qualifier(value="xxx")去配合使用，指定唯一的bean使用。

   ![image-20210206110559606](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@cf11908ba603cf0e0666d87a6d1875ec5c44f4bb/2021/03/03/473ebb27d0c32296c725cd1a4843e061.png)

   **@Resource注解**

   ```java
   public class People{    @Resource(name = "cat2")    private Cat cat;        @Resource    private Dog dog;}
   ```

   两者区别：

   - 都是用来装配的，都可以放在属性字段上
   - @Autowired通过byName的实现【常用】
   - @Resource默认通过byName的方式实现，如果找不到名字，则通过byType实现！如果两个都找不到就报错！【常用】
   - 执行顺序不同：@ Autowired通过byType的方式实现。@ Resource默认通过byname的方式实现。

### 8.使用注解开发

在Spring4之后，要使用注解开发，必须要保证aop的导入了

使用注解需要导入context约束，增加注解的支持 ！！！

```xml
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xmlns:context="http://www.springframework.org/schema/context"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd        http://www.springframework.org/schema/context        https://www.springframework.org/schema/context/spring-context.xsd">    <context:annotation-config/><!--    指定扫描访问的包-->    <context:component-scan base-package="com.luo"/><!--    <bean id="user" class="com.luo.pojo.User"/>--></beans>
```

##### 1.属性的注入

```java
//下面这样搞就直接等价于<bean id="user" class="com.luo.pojo.User"/>@Component@Scope("prototype")public class User {    @Value("小明")    public String name;    @Value("kk")    public void setName(String name) {        this.name = name;    }
```

##### 2.衍生的注解

@Component有几个衍生注解，我们在web开发中，会按照MVC三层架构分层！

- dao【@Repository】

- service【@Service】

- controller【@Controller】
- ​	这四个注解功能都是一样的，都是代表将某个类注解到Spring中华，装配Bean

##### 3.自动装配置

```java
@Autowired:自动装配通过类型。名字如果@Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx")@Nu11able:字段标记了这个注解，说明这个字段可以为nu11;@Resource:自动装配通过名字。类型。
```

##### 4.作用域

```
@Scope("prototype")
```

##### 5.小结

xml与注解:

- xml更加万能，适用于任何场合!维护简单方便

- 注解不是自己类使用不了，维护相对复杂!

xml与注解最佳实践:

- xml用来管理bean;

- 注解只负责完成属性的注入

- 我们在使用的过程中，只需要注意一个问题：必须让注解生效，就需要开启注解的支持！

### 9.使用Java的方式配置Spring

  JavaConfig是Spring的一个子项目，在Spring4之后，它成为了一个核心功能！

实体类

```java
package com.luo.pojo;import org.springframework.beans.factory.annotation.Value;import org.springframework.stereotype.Component;@Componentpublic class User {    private String name;    public String getName() {        return name;    }    @Value("小小小")    public void setName(String name) {        this.name = name;    }    @Override    public String toString() {        return "User{" +                "name='" + name + '\'' +                '}';    }}
```

配置文件

```java
@Configuration //这个会被Spring容器托管，注册到容器中，因为他本来就是一个@Component组件//它就是相当于beans.xml@ComponentScan("com.luo.pojo")@Import(LuoConfig2.class)public class LuoConfig {    //注册一个bean,这个方法的名字就相当于bean的id属性，方法的返回值    @Bean    public User getUser(){        return new User();    }}
```

测试类

```java
public static void main(String[] args) {    //如果完全使用了配置类方式去做，我们就只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载    ApplicationContext context = new AnnotationConfigApplicationContext(LuoConfig.class);    User user =  context.getBean("getUser",User.class);    System.out.println(user.getName());}
```

这种纯java的配置方式在springboot中随处可见

### 10.代理模式

![image-20210206211147061](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@e455a5d822f1ac3d10e00b5c97849326f33382a0/2021/03/03/aa2866cc5132a8f051be017a683da147.png)

**SpringAOP的底层！**

##### 1.分类

###### 1.静态代理

![image-20210206223007501](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@e20cf343a7ad8561e3fb7c879cc56a0be7e5a96b/2021/03/03/43e2c0c89278cd2274a75437c63517e9.png)

1.角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
- 客户：访问代理对象的人！

2.代理模式的好处：

- 可以使真实角色的操作更加纯粹！不用去关注一些公共的业务
- 公共也就就交给代理角色！实现了业务的分工！
- 公共业务发生扩展的时候，方便集中管理！

3.缺点：

- 一个真实角色就会产生一个代理角色;代码量会翻倍~开发效率会变低~

4.代码步骤：

1. 接口
2. 真实角色
3. 代理角色
4. 客户端访问代理角色

###### 2.动态代理

   1. 动态代理和静态代理角色一样

   2. 动态代理的代理类是动态生成的，不是我们直接写好的！

   3. 动态代理分为两大类：基于接口的动态代理，基于类的动态代理

      - 基于接口 --- JDK动态代理
      - 基于类:cglib
      - java字节码实现：javasist

      需要了解两个类：Proxy:代理,InvocationHandler:调用处理

​    1.好处：

- 静态代理的好处它都占有！！！
- 一个动态代理类代理的是一 个接口，一般就是对应的一类业务。
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可!

### 11.AOP（面向切面编程！！！）

##### 1.什么是AOP？

AOP (Aspect Oriented Programming)意为:面向切面编程，通过预编译方式和运行期动态代理实现程序功能
的统一维护的一 种技术。AOP是OOP的延续及补充，是软件开发中的一个热点，也是Spring框架中的一一个重要内容,是
函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离,从而使得业务逻辑各部分之间的耦合
度降低，提高程序的可重用性，同时提高了开发的效率。**AOP的编程模式就是横向的编程！！**

![image-20210207094414012](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@967d34b4be42bc4c19ae4e9c4711ba68234bf056/2021/03/03/eaa8fb5e83ce81dbc7f6b95a3aebc4ad.png)

##### 2.为什么需要AOP？

想象下面的场景，开发中在多个模块间有某段重复的代码，我们通常是怎么处理的？显然，没有人会靠“复制粘贴”吧。在传统的面向过程编程中，我们也会将这段代码，抽象成一个方法，然后在需要的地方分别调用这个方法，这样当这段代码需要修改时，我们只需要改变这个方法就可以了。然而需求总是变化的，有一天，新增了一个需求，需要再多出做修改，我们需要再抽象出一个方法，然后再在需要的地方分别调用这个方法，又或者我们不需要这个方法了，我们还是得删除掉每一处调用该方法的地方。**实际上涉及到多个地方具有相同的修改的问题我们都可以通过 AOP 来解决**。

##### 3.AOP在Spring中的作用

<u>**提供声明式事务：允许用户自定义切面**</u>

###### 1.AOP 领域中的特性术语：

- 通知（Advice）: AOP 框架中的增强处理。通知描述了切面何时执行以及如何执行增强处理。
- 连接点（join point）: 连接点表示应用执行过程中能够插入切面的一个点，这个点可以是方法的调用、异常的抛出。在 Spring AOP 中，连接点总是表示一个方法的执行。
- 切点（PointCut）: 可以插入增强处理的连接点。
- 切面（Aspect）: 切面是通知和切点的结合。
- 引入（Introduction）：引入允许我们向现有的类添加新的方法或者属性。
- 织入（Weaving）: 将增强处理添加到目标对象中，并创建一个被增强的对象，这个过程就是织入。

![image-20210207122105772](https://cdn.jsdelivr.net/gh/luoqianyi/staticForimages@2de28e462a7e475d91bb251d92eabf4fed8e5261/2021/03/03/bf449cfdd3d5246e359efb371fc143ab.png)

###### 2.名词解释

**关注点**：增加的某个业务。如日志、安全、缓存、事务等。

**通知类型:**

- 前置通如(Before advice) :在某连接点之前执行的通知，但这个通知不能阻止连接点之前的执行流程(除非它抛出- -个异常)。
- 后置通知(After returning advice) :在某连接点正常完成后执行的通知:例如，-一个方法没有抛出任何异常，正常返回。
- 异常通知(After throwing advice) :在方法抛出异常退出时执行的通知。
- 最終通知(After (finally) advice) :当某连接点退出的时候执行的通知(不论是正常返回还是异常退出)。
- 环绕通知(Around Advice) :包围- -个连接点的通知，如方法调用。这是最强大的一-种通知类型。环绕通知可以在方法调用前后完成自定义的行为。它也会选择是否继续执行连接点或直接返回它自己的返回值或抛出异常来结束执行。

###### 3.使用Spring实现AOP

1. 使用SpringAPI来实现

   - 首先导入依赖

   ```xml
   <!--        使用SpringAop的时候需要导入-->        <dependency>            <groupId>org.apache.geronimo.bundles</groupId>            <artifactId>aspectjweaver</artifactId>            <version>1.6.8_2</version>        </dependency>
   ```

   - Log.java前置通知

   ```java
   public class Log  implements MethodBeforeAdvice {    public void log(){        System.out.println("进入了执行方法");    }    public void before(Method method, Object[] objects, Object o) throws Throwable {        log();        System.out.println(o.getClass().getName()+"的"+method.getName()+"方法");    }}
   ```

   - AfterLog.java后置通知

   ```java
   public class AfterLog implements AfterReturningAdvice {    /**     * 目标方法执行后的通知     * o:返回值     * method:被调用的方法对象     * objects:被调用的方法对象的参数     * o1:被调用的方法对象的目标对象     */    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {        System.out.println(o1.getClass().getName()+"的"+method.getName()+"被成功执行，返回值是"+o);    }}
   ```

   - 目标类

   ```java
   public class UserServiceImp implements UserService{    public void add() {        System.out.println("增加用户");    }    public void delete(int n) {        System.out.println("删除用户");    }    public void update() {        System.out.println("修改用户");    }    public void search() {        System.out.println("查询用户");    }}
   ```

   - 配置文件

   ```xml
     <?xml version="1.0" encoding="UTF-8"?>  <beans xmlns="http://www.springframework.org/schema/beans"         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"         xmlns:context="http://www.springframework.org/schema/context"         xmlns:aop="http://www.springframework.org/schema/aop"         xsi:schemaLocation="http://www.springframework.org/schema/beans          https://www.springframework.org/schema/beans/spring-beans.xsd          http://www.springframework.org/schema/context          https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">      <bean id="userService" class="com.luo.service.UserServiceImp"/>      <bean id="log" class="com.luo.log.Log"/>      <bean id="afterLog" class="com.luo.log.AfterLog"/>      <aop:config>          <aop:pointcut id="pointcut" expression="execution(* com.luo.service.UserServiceImp.*(..))"/>          <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>          <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>      </aop:config>  </beans>
   ```

Spring-aop就是将公共的业务（如日志、安全等）和领域业务结合，当执行领域业务的时候加入公共业务。实现公共业务的重复利用，其本质还是动态代理。

2. 自定义类来实现aop

   - 首先编写自定义类，里面包含了一些需要插入的方法

     ```java
     public class DiyPointCut {    public void before(){        System.out.println("=========方法执行前======");    }    public void after(){        System.out.println("========方法执行后=====");    }}
     ```

   - 然后编写配置文件

     ```xml
         <bean id="diy" class="com.luo.diy.DiyPointCut"/>    <aop:config><!--        自定义切面，ref:要引入的类-->        <aop:aspect ref="diy">            <aop:pointcut id="point" expression="execution(* com.luo.service.UserServiceImp.*(..))"/>            <aop:before method="before" pointcut-ref="point"/>            <aop:after method="after" pointcut-ref="point"/>        </aop:aspect>    </aop:config>
     ```

3. 使用注解方式实现Aop

   - 开启注解支持

     ```xml
     <!--    开启注解支持-->    <aop:aspectj-autoproxy/>
     ```

   - 编写公共业务类（定义切面）

     ```java
     @Aspect //标注这个类是一个切面public class AnnotationPointCut {    @Before("execution(* com.luo.service.UserServiceImp.*(..))")    public void before(){        System.out.println("--方法使用前--");    }    @After("execution(* com.luo.service.UserServiceImp.*(..))")    public void after(){        System.out.println("--方法执行后--");    }    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点    @Around("execution(* com.luo.service.UserServiceImp.*(..))")    public void around(ProceedingJoinPoint joinPoint) throws Throwable {        System.out.println("环绕前");       Signature signature = joinPoint.getSignature();        System.out.println(signature.toString());;        //执行方法      Object proceed =  joinPoint.proceed();        System.out.println("环绕后");        System.out.println(proceed);    }}
     ```

   - 增加bean

     ```xml
     <bean id="annotation" class="com.luo.diy.AnnotationPointCut"/>
     ```

### 12.整合Mybatis

1. 添加依赖

   ```xml
    <dependencies>        <dependency>            <groupId>junit</groupId>            <artifactId>junit</artifactId>            <version>4.13</version>        </dependency>        <dependency>            <groupId>mysql</groupId>            <artifactId>mysql-connector-java</artifactId>            <version>8.0.22</version>        </dependency>        <dependency>            <groupId>org.mybatis</groupId>            <artifactId>mybatis</artifactId>            <version>3.5.2</version>        </dependency>        <dependency>            <groupId>org.springframework</groupId>            <artifactId>spring-webmvc</artifactId>            <version>5.3.3</version>        </dependency><!--        spring操作数据库的话还需要一个spring-jdbc-->        <dependency>            <groupId>org.springframework</groupId>            <artifactId>spring-jdbc</artifactId>            <version>5.3.3</version>        </dependency>        <!--        使用SpringAop的时候需要导入-->        <dependency>            <groupId>org.apache.geronimo.bundles</groupId>            <artifactId>aspectjweaver</artifactId>            <version>1.6.8_2</version>        </dependency>        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->        <dependency>            <groupId>org.mybatis</groupId>            <artifactId>mybatis-spring</artifactId>            <version>2.0.6</version>        </dependency>    </dependencies>
   ```

2. 编写数据源配置

   mybatis中：

   ```xml
   <configuration><!--    <properties resource="db.properties"/>-->    <settings>        <!--        这是一个标准日志工厂的实现-->        <setting name="logImpl" value="LOG4J2"/>        <setting name="mapUnderscoreToCamelCase" value="true"/>    </settings>    <typeAliases>        <package name="com.luo.pojo"/>    </typeAliases>     <mappers>        <mapper class="com.luo.mapper.UserMapper"/>    </mappers></configuration>
   ```

   spring配置文件中连接数据库：

   ```xml
   <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>    <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true&amp;serverTimezone=UTC"/>    <property name="password" value="luoqianyi"/>    <property name="username" value="root"/></bean>
   ```

3. sqlSessionFactory

   ```xml
   <!--    sqlSessionFactory-->    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">        <property name="dataSource" ref="dataSource"/><!--        绑定Mybatis配置文件-->        <property name="configLocation" value="classpath:mybatis-config.xml"/><!--        <property name="mapperLocations" value="classpath:com/luo/mapper/*.xml"/>-->    </bean>
   ```

4. sqlSessionTemplate(spring-mybatis的核心)

   ```xml
   <!--    sqlSession-->    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate"><!--        只能使用构造器注入sqlSession，因为它没有set方法-->        <constructor-arg index="0" ref="sqlSessionFactory"/>    </bean>
   ```

   注意这里的sqlSession就是sqlSessionTemplate,而这个对象需要添加至少一个参数

5. 需要给接口加实现类

   ```java
   public class UserMapperImpl implements UserMapper {    private SqlSessionTemplate sqlSessionTemplate;    //我们的所有操作，都是使用sqlSession来执行。在原来，现在都是用SqlSessionTemplate    public List<User> selectUser() {       UserMapper user = sqlSessionTemplate.getMapper(UserMapper.class);       return user.selectUser();    }    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate){        this.sqlSessionTemplate = sqlSessionTemplate;    }}
   ```

6. 将自己写的实现类（实现类里面去做mybatis中做的事情），注入到Spring中

   ```xml
   <bean id="usermapper" class="com.luo.mapper.UserMapperImpl">    <property name="sqlSessionTemplate" ref="sqlSession"/></bean>
   ```

7. 测试使用即可

注意：新增的spring-mybatis中有个简化接口实现类代码的方法，利用SqlSessionDaoSupport类来进行简化

```java
public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {    public List<User> selectUser() {        return getSqlSession().getMapper(UserMapper.class).selectUser();    }}
```

同时省去构造器注入sqlSession的操作，只需要在bean.xml中装配如下即可：

```xml
<bean id="usermapper2" class="com.luo.mapper.UserMapperImpl2">    <property name="sqlSessionFactory" ref="sqlSessionFactory"/></bean>
```

### 13.声明式事务

事务在项目开发中，十分的重要，涉及到一些数据的一致性问题，不能马虎！确保一致性和完整性，要么都失败，要么都成功。

事务的ACID原则：原子性、一致性、隔离性、持久性

隔离性：多个业务可能操作同一个资源，防止数据损坏

持久性：事务一旦提交，无论发生什么情况，结果都不会被影响，被持久化的写到存储器中！

##### 1.交由容器管理事务

1. 配置声明式事务

   ```xml
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  <constructor-arg ref="dataSource" /></bean>
   ```

2. 结合AOP实现事务的织入

   ```xml
   <!--配置事务通知--><tx:advice id="txAdvice" transaction-manager="transactionManager">	<!--给哪些方法配置事务,可以配置事务的传播特性：new propagation-->    <tx:attributes>    	<tx:method name="add" propagation="REQUIRED"/>        <tx:method name="delete" propagation="REQUIRED"/>        <tx:method name="query" read-only="true"/>        <tx:method name="*"/>    </tx:attributes></tx:advice>
   ```

3. 配置事务切入

   ```xml
   <aop:config>	<aop:pointcut id="txPoint" expression="execution(* com.luo.mapper.*.*(..))"/>    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/></aop:config>
   ```

##### 2.编程式事务管理

MyBatis 的 `SqlSession` 提供几个方法来在代码中处理事务。但是当使用 MyBatis-Spring 时，你的 bean 将会注入由 Spring 管理的 `SqlSession` 或映射器。也就是说，Spring 总是为你处理了事务。

你不能在 Spring 管理的 `SqlSession` 上调用 `SqlSession.commit()`，`SqlSession.rollback()` 或 `SqlSession.close()` 方法。如果这样做了，就会抛出 `UnsupportedOperationException` 异常。在使用注入的映射器时，这些方法也不会暴露出来。

无论 JDBC 连接是否设置为自动提交，调用 `SqlSession` 数据方法或在 Spring 事务之外调用任何在映射器中方法，事务都将会自动被提交。

如果你想编程式地控制事务，请参考 [the Spring reference document(Data Access -Programmatic transaction management-)](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#transaction-programmatic) 。下面的代码展示了如何使用 `PlatformTransactionManager` 手工管理事务。

```java
public class UserService {  private final PlatformTransactionManager transactionManager;  public UserService(PlatformTransactionManager transactionManager) {    this.transactionManager = transactionManager;  }  public void createUser() {    TransactionStatus txStatus =        transactionManager.getTransaction(new DefaultTransactionDefinition());    try {      userMapper.insertUser(user);    } catch (Exception e) {      transactionManager.rollback(txStatus);      throw e;    }    transactionManager.commit(txStatus);  }}
```

在使用 `TransactionTemplate` 的时候，可以省略对 `commit` 和 `rollback` 方法的调用。

```java
public class UserService {  private final PlatformTransactionManager transactionManager;  public UserService(PlatformTransactionManager transactionManager) {    this.transactionManager = transactionManager;  }  public void createUser() {    TransactionTemplate transactionTemplate = new TransactionTemplate(transactionManager);    transactionTemplate.execute(txStatus -> {      userMapper.insertUser(user);      return null;    });  }}
```

