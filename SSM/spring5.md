# spring概述

轻量级（体积小，jar包少）的，开源的javaEE框架

spring的两个核心部分：ioc aop





# hello，spring

导入spring的四个核心jar包

- beans
- context
- core
- expression

还需要导入commons_loggin.jar（不属于spring）







# IOC

全称为inversion of control，控制反转，作用为解耦合

例如web阶段我们写service层，总要new一个dao类，这样耦合度就很高，ioc的思路是，将我们要创建的对象先写在xml文件里，然后通过spring提供的容器/对象工厂，解析该xml文件，并帮我们创建相应的对象实例



## spring工厂

spring的工厂为两个接口

- **BeanFactory** 加载配置文件时不会创建对象，只有在获取对象时才会创建对象
- **ApplicationContext** BeanFactory的子接口，面向开发人员，在加载配置文件的时候就会创建对象



spring工厂接口的实现类

- **FileSystemXmlApplicationContext** 路径填写磁盘路径
- **ClassPathXmlApplicationContext** 路径填写类路径，src为根路径





## Bean管理

bean管理指两个操作

- 用spring创建对象，默认调用无参构造器
- 用spring给对象注入属性（操作对象属性）







### 基于xml



#### 创建对象

1. 在spring的xml文件中设置bean标签

   ```xml
   <bean id="user" class="com.origin.firstSpring.User"></bean>
   ```

​	bean标签中的常用属性：id唯一标识  class类的全路径 name与id相同，基本不使用

2. 通过工厂创建对象

   ```java
   //创建工厂，解析beans.xml文件
   ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   //使用getBean方法，传入id，类的class对象创建对象实例
   User user = context.getBean("user", User.class);
   ```

   



#### 注入属性

DI译为依赖注入，就是注入属性的意思



通过在bean标签内部设置propertry标签注入属性，name为属性名，value为要设置的属性值，要求该类必须有该属性对应的set方法

```xml
<bean id="user" class="com.origin.firstSpring.User">
    <property name="num1" value="12"></property>
</bean>
```



通过在bean标签内部设置constructor-arg标签来给构造器传参，要求必须要有对应的构造器存在

```xml
<bean id="user" class="com.origin.firstSpring.User">
    <constructor-arg name="num1" value="10"></constructor-arg>
    <constructor-arg name="num2" value="20"></constructor-arg>
</bean>
```







##### 几种特殊的属性注入

注入null

```xml
<!--注入null-->
<property name="num2">
    <null/>
</property>
```





注入外部bean

```xml
<bean id="user" class="com.origin.firstSpring.User">
    <!--注入外部的bean对象时使用ref来代替value，填入的是外部bean的id-->
    <constructor-arg name="cat" ref="cat01"></constructor-arg>
    <constructor-arg name="num1" value="10"></constructor-arg>
    <constructor-arg name="num2" value="20"></constructor-arg>
</bean>
<bean id="cat01" class="com.origin.firstSpring.Cat">
    <constructor-arg name="name" value="橘猫猫"></constructor-arg>
    <constructor-arg name="age" value="1"></constructor-arg>
</bean>
```





注入内部bean，直接在constructor或property内部创建一个bean并注入

```xml
<bean id="user" class="com.origin.firstSpring.User">
    <constructor-arg name="cat">
        <bean id="cat01" class="com.origin.firstSpring.Cat">
            <constructor-arg name="name" value="橘猫猫"></constructor-arg>
            <constructor-arg name="age" value="1"></constructor-arg>
        </bean>
    </constructor-arg>
    <constructor-arg name="num1" value="10"></constructor-arg>
    <constructor-arg name="num2" value="20"></constructor-arg>
</bean>
```





级联赋值，即给属性的属性进行注入，注意需要有get方法

```xml
<bean id="user" class="com.origin.firstSpring.User">
    <property name="num1" value="10"></property>
    <property name="num2" value="20"></property>
    <property name="cat" ref="cat"></property>
    <!--给user中的cat的name和age属性注入-->
    <property name="cat.name" value="橘猫猫"></property>
    <property name="cat.age" value="1"></property>
</bean>

<bean id="cat" class="com.origin.firstSpring.Cat"></bean>
```



注入数组

```xml
<!--这里的courses属性是个String数组-->
<property name="courses">
    <array>
        <value>橘猫猫</value>
        <value>橙猫猫</value>
    </array>
</property>
```





注入list

```xml
<property name="list">
    <list>
        <value>大大怪</value>
        <value>小小怪</value>
    </list>
</property>
```





注入map

```xml
<property name="map">
    <map>
        <entry key="001" value="美人鱼战士"></entry>
        <entry key="002" value="企鹅男孩"></entry>
    </map>
</property>
```





如果数组或者集合中保存的是对象，那么将value替换为ref，bean属性为要注入的bean的id

```xml
<property name="cats">
    <list>
        <ref bean="cat02"></ref>
        <ref bean="cat03"></ref>
        <ref bean="cat04"></ref>
    </list>
</property>
```





设置公用的list，先在xml文件中加入util命名空间，使能够使用util标签

```xml
xmlns:util="http://www.springframework.org/schema/util"
xsi:schemaLocation="http://www.springframework.org/schema/util 			     http://www.springframework.org/schema/util/spring-util.xsd">
```



设置公共list

```xml
<util:list id="list">
    <value>橘猫猫2</value>
    <value>橙猫猫2</value>
</util:list>
```



注入公共list

```xml
<property name="list" ref="list">
```





#### xml自动装配

通过property注入属性叫做手动装配，bean标签中的autowire属性可以实现自动注入属性，autowire有两个值

- byname 自动匹配本xml中的id值与属性名相同的bean并注入
- bytype 自动匹配本xml中的对象类型与属性类型相同的bean并注入







#### 外部属性注入

先来看使用spring的xml方式来获取druid的datasource

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:13306/SnowFish?rewriteBatchedStatements=true&amp;useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true
                                "></property>
    <property name="username" value="root"></property>
    <property name="password" value="origin"></property>
</bean>
```



这种方式是一种写死的方式，我们希望在spring的xml中读取外部的配置文件来生成dataSource的bean

首先引入名称空间context，然后创建一个properties文件，设置如下context标签引入该properties文件

```xml
<context:property-placeholder location="classpath*:druid.properties"/>
```



在创建datasource的bean标签时使用类似于el表达式的表达式引入properties文件中相应的值

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${prop.driverClassName}"></property>
    <property name="url" value="${prop.url}"></property>
    <property name="username" value="${prop.username}"></property>
    <property name="password" value="${prop.password}"></property>
</bean>
```





### 基于注解



#### 创建对象

spring中用于创建对象的注解有：

- Componet
- Service
- Controller
- Repository

这几个注解并没有实质的区别，只是为了我们在开发的时候有更好的可读性



想要使用spring注解，首先引入aop.jar

然后开启组件扫描，即告诉spring扫描哪些包并检查有无注解：在xml中设置以下context标签

```xml
<context:component-scan base-package="com.origin.packages"></context:component-scan>
```

这里代表扫描packages包以及其子包中是否含有注解，如果想扫描多个包，可以在包名间使用逗号分隔



接下来在我们创建的类中加上注解即可，这里的value相当于bean标签中的id，value有默认值，因此可以忽略不写，默认值为首字母小写的类名

```java
@Component(value = "myClass")
public class MyClass {
}
```



接下来与xml方式相同，创建工厂类，通过工厂类的getBean方法获取对象





#### 扫描配置

上面说的组件扫描实际上就是用filter去拦截指定的类，默认情况下的filter会拦截我们指定的包下的所有类，我们可以配置使拦截指定的类



例如这里就是表示只拦截注解为Component的类

```xml
<context:component-scan base-package="com.origin.packages" use-defaultfilters="false">  	<context:include-filter type="annotation" 										expression="org.springframework.stereotype.Component"/>
</context:component-scan>
```

上面的context:include可以替换成context-exclude，表示不扫描这些类







#### 属性注入



**AutoWired** 根据属性类型注入

保证需要属性注入的类和要注入的类都添加上注解后，在要进行注入的属性前加注解

```java
@Service
public class TestService {
	//该注解通过类型匹配注入TestDao，前提是这个TestDao是添加了注解的
    @Autowired
    private TestDao testDao;

    public void option() {
        testDao.add();
    }
}
```



**Qualifier** 根据属性名称注入

Qualifier需要和AutoWired一起使用，写在AutoWired标签的下面，括号内填入要注入的类的value（默认首字母小写的类名）

```java
@Autowired
@Qualifier(value = "testDaoImpl")
private TestDao testDao;
```



Resource 可以根据类型注入，也可以根据名称注入,即可以当成AutoWired用， 也可以当成Qualifier用

```java
//根据名称注入
@Resource(name = "testDaoImpl")
private TestDao testDao;

//根据类型注入
@Resource
private TestDao testDao;
```



Value 注入普通属性

```java
@Value(value = "hello")
private String string;
```







#### 完全注解开发

我们上述的注解开发仍然需要一个xml文件来配置组件扫描，现在我们来实现纯注解开发

首先创建一个类，用作配置类，给该类加上configurtation注解表示该类是配置类，加上ComponentScan注解以配置组件扫描

```java
@Configuration
@ComponentScan(basePackages = {"com.origin"})
public class SpringConfig {
}
```



接下来在创建spring工厂时不再通过加载xml文件创建，而是通过加载配置类来创建

```java
ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
```





## spring内置bean

spring有两种bean，一种是我们自己创建的普通bean，还有一种是spring内置的工厂bean



实现了spring中的factoryBean接口的bean类就可以称为工厂bean

```java
public class MyFactory implements FactoryBean {}
```



该接口中有一个方法,该方法的返回类型T就代表了该使用工厂类获取该bean对象时获取到的对象类型，即如果该方法返回了一个String对象实例，那么调用getBean方法，返回的就是该String对象实例

```java
T getObject() throws Exception;
```









## Bean的作用域

默认情况下，spring的bean标签是单实例的，即每次getBean获取的都是同一个对象实例

bean标签的scope属性可以设置该bean是单实例还是多实例，scope有以下值：

- singleton 默认值，表示单实例，此时在解析xml文件时就会创建好这个唯一的对象实例
- prototype 表示多实例，解析xml时不创建对象，每次调用getBean方法时创建一个新对象实例





## bean的生命周期

bean标签创建对象后执行的代码的顺序：

1. 调用构造器
2. 调用property标签
3. 调用后置处理器的postProcessBeforeInitialization方法
4. 调用init方法，在bean标签的init-method属性中配置方法名
5. 调用后置处理器的postProcessAfterInitialization方法
6. 操作对象
7. 调用工厂类的close方法后，对于单实例bean，调用destroy方法，在bean标签的destroy-method属性中配置方法名



注意：

init方法和destroy方法是我们自己设计的，bean类中的任意方法都可以是init方法和destroy方法

实现了spring中BeanPostProcessor接口的类就可以称为后置处理器，后置处理器类和普通类一样需要在xml中配置bean标签，配置完之后，所有的普通bean类在创建时，都会调用后置处理器的两个方法













# AOP

aop译为面向切面编程，简单来说就是，给一个模块添加或者删除功能不需要修改源代码



## 代理模式

aop的底层原理用到了代理模式，因此这里先说明代理这个设计模式



首先有一个接口Teacher，以及其实现类TeacherImpl

```java
public interface Teacher {
    public void teach();

    public void sayHello(String name);
}
```



```java
public class TeacherImpl implements Teacher{
    @Override
    public void teach() {
        System.out.println("teaching...");
    }

    @Override
    public void sayHello(String name) {
        System.out.println("hello " + name);
    }
}
```



### 静态代理

代理的思路是：不直接通过TeacherImpl对象实例来调用其方法，而是通过创建一个TeacherImpl类的代理类来调用其方法，并添加其他的业务代码



对于TeacherImpl的代理类，我们让其也实现Teacher接口，这样该代理类就有和目标类相同的方法，同时，在代理类中维护了一个目标类的对象引用，在创建代理类时，通过构造器给这个目标类的对象实例赋值，这样，代理类就有了一个目标类的对象实例，每次调用方法时，调用代理类的相应方法，在代理类的相应方法中通过目标类对象调用目标类的相应方法，同时，我们还可以额外添加一些新的业务代码。

```java
public class TeacherImplProxy implements Teacher{

    private Teacher teacher;

    public TeacherImplProxy(Teacher teacher) {
        this.teacher = teacher;
    }

    @Override
    public void teach() {
        System.out.println("添加的业务");
        teacher.teach();
    }

    @Override
    public void sayHello(String name) {
        System.out.println("添加的业务");
        teacher.sayHello(name);
    }
}
```





```java
Teacher teacher = new TeacherImpl();
TeacherImplProxy teacherImplProxy = new TeacherImplProxy(teacher);
teacherImplProxy.teach();
```





### 动态代理

动态代理与静态代理的区别在于，动态代理通过设计一个代理工厂类来得到目标对象的代理对象,这样就不需要为每个目标类定义一个代理类了,直接由一个统一的代理工厂来得到不同类的代理类

代理工厂类ProxyFactory，该类用以生成任意类的代理类

```java
public class ProxyFactory {
    //维护的目标类，即TeacherImpl
    private Object target;

    //通过构造器将TeacherImpl对象实例传入
    public ProxyFactory(Object target) {
        this.target = target;
    }

    //getProxyInstance方法用以给target生成一个代理对象
    public Object getProxyInstance() {
        //使用了java.lang包下的proxy类，该类的newProxyInstance静态方法可以返回一个指定类的代理	类，需要传入该类的类加载器，该类实现的所有接口，以及一个监听器InvocationHandler，这个InvocationHandler实际上是一个接口，因此这里使用匿名内部类的方式直接创建一个实现了该接口的匿名对象实例
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("start proxy...");
                Object ret = method.invoke(target, args);
                System.out.println("close...");
                return ret;
            }
        });
    }
}
```



通过代理工厂类得到的代理对象来调用目标对象的方法，这里得到的代理对象proxyInstance的运行类型实际上是proxy，proxy对象维护了一个监听器，也就是上面传进去的InvocationHandler，每次通过代理对象调用方法时，会先进入监听器的invoke方法中，该方法的参数method就是此次的方法对象，args就是方法的参数，因此我们所添加的业务代码就可以写在invoke方法中

```java
TeacherImpl teacher = new TeacherImpl();
ProxyFactory proxyFactory = new ProxyFactory(teacher);
Teacher proxyInstance = (Teacher) proxyFactory.getProxyInstance();
proxyInstance.sayHello("jack");
```





### cglib代理

静态代理需要目标类实现某个接口，同时代理类也实现相同的接口；动态代理需要目标类实现某个接口

cglib代理则不需要目标类必须实现某个接口





## aop术语

- 连接点 类里面的哪些方法可以被扩展，这些方法就叫做连接点
- 切入点 一个类里面有很多方法可以被增强，实际被真正增强的方法称为切入点
- 通知(增强) 实际增强的逻辑代码部分叫做通知，通知有多种类型
  - 前置通知 Before 在目标方法之前执行
  - 后置通知 AfterReturning 在目标方法return之前执行
  - 环绕通知 Around 目标方法可以放在通知内的任何位置
  - 异常通知 AfterThrowing 目标方法中发生了异常后执行
  - 最终通知 After 在目标方法return之后执行
- 切面 一个动作，指把通知加入切入点的这一动作





## aop操作

spring的aop操作一般基于AspectJ来实现，AspectJ不是spring的组成部分，是一个独立的框架



首先引入spring中的相关依赖aop.jar，aspects.jar

还需要引入AspectJ这个框架

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220601174229652.png" alt="image-20220601174229652" style="zoom:50%; margin-left: 0;" />





### 切入点表达式

切入点表达式用于确定对哪个类的哪个方法进行增强

其语法结构为：

```
execution(修饰符 返回类型 切入点的全类名.切入点方法名(参数) 异常抛出)
```

修饰符：可选，表示该方法的访问修饰符

返回类型：必填，表示该方法的返回类型，使用`*`来匹配所有的返回类型

切入点的类：可选，切入点的全类名

切入点方法：必填，指定要匹配的方法名，可以使用*匹配所有方法，支持两个通配符`*`和`..`

*表示一个任意类型的参数，..表示零个或多个任意类型的参数，例如`(*, String)`表示匹配的方法有两个参数，第一个参数是任意类型，第二个参数是String







### 基于注解

首先给目标类和代理类添加@component注解创建对象

接下来给代理类添加@Aspect注解，表示该类是代理类

在xml中开启AspectJ生成代理对象

```xml
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

上面这一步也可以替换为完全注解开发，即创建配置类

```java
@Configuration
@ComponentScan(basePackages = {"com.origin"})
@EnableAspectJAutoProxy(proxyTargetClass = true)//开启生成代理对象
public class Config {
}
```





给代理类的增强方法添加Before注解，表示该方法是一个前置通知

测试时通过工厂的getBean方法生成类调用方法



目标类

```java
@Component
public class User {
    public void add() {
        System.out.println("add()");
    }
}
```





代理类

```java
@Component
@Aspect//表明该类是代理类
public class UserProxy {
	//Bofore便签表明该方法是前置通知，即在执行目标方法之前执行，excution表达式用于匹配所要代理的方   	  //法，可以是一个也可以是多个
    @Before(value = "execution(public void com.origin.AOP.E01.User.add())")
    public void before() {
        System.out.println("before");
    }
}
```





测试

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
User user = context.getBean("user", User.class);
//编译类型是User
System.out.println(user);
//运行类型并不是User，而是User类的代理对象类，可以看成User类的子类，该子类重写了目标方法，因此可以给目标方法添加新的功能
System.out.println(user.getClass());
user.add();
```





使用Around注解表示环绕通知，环绕通知中可以自动传入一个参数，用该参数可以调用目标方法，实现将目标方法环绕的效果

```java
@Around(value = "execution(public void com.origin.AOP.E01.User.add())")
public void after(ProceedingJoinPoint point) throws Throwable {
    System.out.println("code1");
    point.proceed();//调用目标方法
    System.out.println("code2");
}
```





### 抽取相同的切入点

有时候往往多个通知有相同的切入点，此时就需要对该切入点做抽取

做法是在代理类中定义一个方法，给该方法加上@Pointcut注解，并加入切入点表达式

```java
@Pointcut(value = "execution(public void com.origin.AOP.E01.User.add())")
public void point() {

}
```



这样，接下来我们每次使用该切入点时将切入点表达式替换成该方法的方法名即可

```java
@Before(value = "point()")
public void before() {
    System.out.println("before");
}
```





### 设置代理类的优先级

如果一个目标类有多个代理类，那么就要考虑这些代理类的优先级问题

方法为给代理类加上@Order注解，填入一个非负整数，数字越小表示优先级越高，注意，优先级高并不代表该代理类的所有类型的通知都会先于其他代理类执行，即不管优先级如何，所有的before通知必定先于after通知，而对于所有的before通知，优先级高的先执行







### 基于配置文件



```xml
<!--创建对象-->
<bean id="book" class="com.origin.AOP.E02.Book"></bean>
<bean id="bookProxy" class="com.origin.AOP.E02.BookProxy"></bean>
<!--配置aop-->
<aop:config>
    <!--配置切入点-->
    <aop:pointcut id="point1" expression="execution(public void com.origin.AOP.E02.Book.read())"/>
    <!--配置代理类-->
    <aop:aspect ref="bookProxy">
        <!--配置通知-->
        <aop:before method="pre" pointcut-ref="point1"/>
    </aop:aspect>
</aop:config>
```











# JDBCTemplate

JDBCTemplate是spring对JDBC的封装类，方便实现对数据库的操作



需要引入spring的依赖spring-jdbc.jar, spring-tx.jar(用于事务), spring-orm.jar(用于整合其他框架)

同时需要连接数据库的相关依赖



从前我们Dao层需要调用我们封装好的DruidUtils来获取连接，还需要DBUtils来方便执行sql语句返回封装好的对象，现在我们只需要使用spring为我们提供的JDBCTemplate类即可操作数据库

```java
@Repository
public class BookDaoImpl implements BookDao{
    //dao层维护一个JDBCTemplate类，使用该类可以直接帮我们执行sql，并返回相应值或对象
    @Autowired
    private JdbcTemplate jdbcTemplate;
}
```





## 增删改查

```java
//增删改语句
jdbcTemplate.update(sql, Object...args);
//查询单行单列
jdbcTemplate.queryForObject(sql, Integer.class, args);
//查询一行记录，单个对象
jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), args);
//查询多行记录，对象集合
jdbcTemplate.query(sql, new BeanPropertyRowMapper<User>(User.class), args);
```





## 批量操作

```java
//执行批量添加操作,这里的args是一个参数数组的集合
String sql = "INSERT INTO t_user VALUES(?, ?, ?, ?)";
jdbcTemplate.batchUpdate(sql, List<Object[]> args);
```







# 事务

spring的事务管理底层用的是aop

spring中用于事务操作的是一个接口`PlatformTransactionManager`， 其他框架实现该接口从而使用spring进行事务操作，其中配合jdbcTemplate类使用的实现类为`DataSourceTransactionManager`



首先创建DataSourceTransactionManager类的bean对象，并注入连接池

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="datasource"></property>
</bean>
```



开启以注解方式配置的事务，注入事务管理器

```xml
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```



给需要开启事务的类或方法添加注解，通常在Service层捕获异常

```java
@Service
@Transactional
public class UserServiceImpl implements UserService{
}
```





## Transaction参数



### propagation

事务传播行为，即在事务方法中调用其他方法事务该如何传播

- required
- required_NEW

**isolation**

**timeout**

**readOnly**

**rollbackFor**

**noRollbackFor**





### isolation

多线程下的事务操作可能会导致以下问题

- **脏读** 事务a修改了一个数据,但是未提交,此时事务b读取这个数据,却读取到了a修改后的值
- **不可重复读** 事务a想要针对某一数据在某一指定时间下的内容做一些操作,但是在操作的过程中事务b修改了这个数据并提交了,此时a却读取到了被b修改后的数据,这里强调的是数据的修改
- **幻读** 事务a查询一张表的数据个数,事务还未提交,事务b在该表中添加或删除了数据,此时事务a再次进行了查询,发现查询到的个数与之前不同.这里强调的是数据的新增或删除



通过设置事务的隔离级别来解决或设定以上情况

|                               | 脏读 | 不可重复读 | 幻读 |
| :---------------------------: | :--: | :--------: | :--: |
| **read uncommitted 读未提交** |  √   |     √      |  √   |
|  **read committed 读已提交**  |  ×   |     √      |  √   |
| **repeatable read 可重复读**  |  ×   |     ×      |  √   |
|    **serializable 串行化**    |  ×   |     ×      |  ×   |





### timeout

超时时间，单位是秒，即设置事务执行超过某一时间后进行回滚操作，默认值为-1，即永不超时



### readOnly

设置该事务是否只能进行查询操作，默认是false，即可以dml



### rollbackFor

设置事务中出现了那些异常就进行回滚



### noRollbackFor

设置事务中出现了哪些异常时不进行回滚操作





以上属性在注解中格式为

```java
@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.REPEATABLE_READ, timeout = -1, readOnly = false, rollbackFor = {}, noRollbackFor = {})
```





## 完全注解

使用完全注解来完成事务操作



```java
@Configuration//声明配置类
@ComponentScan(basePackages = {"com.origin"})//配置注解扫描位置
@EnableTransactionManagement//开启事务
public class SpringConfig {

    //bean注解相当于bean标签,用于在配置类中创建对象,标注在方法上,在方法中返回需要创建的对象即可
	//创建DataSource  
    @Bean
    public DataSource getDataSource() throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("src/druid.properties"));
        return DruidDataSourceFactory.createDataSource(properties);
    }

    //创建JdbcTemplate
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource) {//这里会自动根据类型注入上面已经创建好的DataSource
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    //创建DataSourceTransactionmanager
    @Bean
    public DataSourceTransactionManager getDatasourceTransactionManager(DataSource dataSource) {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }
}

```





# spring5新功能



## 日志

依赖

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220605032536101.png" alt="image-20220605032536101" style="zoom: 50%; margin-left: 0;" />

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220605032612784.png" alt="image-20220605032612784" style="zoom:50%; margin-left: 0;" />





## Nullable注解

Nullable注解可以写在方法或属性上,表示该方法的返回值或该属性的值可以为空,也可以写在方法的参数之前,表示该参数可以为空





## 整合junit



### jUnit4

首先引入依赖

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220605143648441.png" alt="image-20220605143648441" style="zoom:50%;" />	



创建测试类,进行如下配置

```java
@RunWith(SpringJUnit4ClassRunner.class)//整合junit4框架
@ContextConfiguration("classpath:beans.xml")//加载配置文件
public class JTest {

    //注入userService,方便在test方法中直接使用
    @Autowired
    private UserService userService;

    @Test
    public void m1() {
        System.out.println(userService);
    }
}
```





### junit5

```java
@SpringJUnitConfig(locations = "classpath:beans.xml")
class JTest5Test {

    @Autowired
    private UserService userService;


    @Test
    public void m1() {

    }

}
```





