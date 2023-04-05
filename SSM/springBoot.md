>@author origin
>
>@QQ 1223003136
>
>:v::eyes::v:



# springBoot概述

springBoot帮我们完成了spring的各种框架的整合问题,通俗讲,springBoot是框架的框架



# hello, springBoot

首先创建maven工程

在pom.xml中设置如下parent标签

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.1</version>
</parent>
```



再添加一个依赖spring-boot-starter-web,添加该依赖就相当于添加了我们编写web应用所需要的所有依赖:happy:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```



接下来就可以写我们的业务代码了,例如写一个spring mvc中的控制器

```java
@Controller
public class Controller01 {

    @RequestMapping("/test01")
    @ResponseBody
    public String test01() {
        return "<h1>hello, spring Boot 2 !</h1>";
    }
}
```



想要运行我们的项目,也不要我们自己配置服务器了,创建一个类,用@SpringBootApplication注解声明,运行如下主方法即可

```java
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```



注意MainApplication类的位置,只有主程序所在的包及其子包才会被扫描注解

<img src="../../images/Snipaste_2022-07-15_21-44-13.png" style="zoom: 50%; margin-left: 0px;">





# 依赖管理

基于springBoot的maven项目,其pom.xml中都会有一个默认的parent坐标

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.1</version>
</parent>
```

这个spring-boot-starter-parent还继承自一个坐标

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.7.1</version>
</parent>
```

这个spring-boot-dependencies中,有一个`<dependencyManagement>`标签,该标签的作用是声明许多`<dependency>`而并不实际引入这些依赖,当我们在子项目中添加了这些依赖而不写版本号的时候,就会自动继承该父项目中`<dependencyManagement>`声明的版本号,从而实现父项目对子项目的依赖版本管理

spring在这个`<dependencyManagement>`中声明了我们可能用到的所有依赖,同时创建了一个`<properties>`声明了所有这些依赖的版本号

同时,springBoot还对一些常用场景的相关依赖进行了整合并添加进了spring-boot-dependencies,例如

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

我们只要在项目引入这个依赖,maven就会自动引入继承自spring-boot-dependencies的spring-boot-starter-web依赖整合包,这个整合包里引入了所有web下会用到的依赖,这就是为什么我们只要这引入一个依赖就可以进行web开发了



# 底层注解





## @Configuration

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";

    boolean proxyBeanMethods() default true;
}
```



标注于类上,用于表示该类是一个配置类,配置类中的方法可以加上@Bean注解,表示将该方法的返回值放入容器中,例如

```java
@Configuration
public class Config01 {

    @Bean
    public Cat cat() {
        return new Cat("tomcat");
    }
}
```



@Configuration注解的proxyBeanMethods属性表示是否将该类变为代理类

如果是代理类,那么@Bean方法每次返回的都是同一个对象实例





## @Conditional

@Conditional是条件装配注解,是一个根注解,有许多子注解,例如@ConditionalOnBean

```java
@Configuration()
public class Config01 {

    //@Bean
    public Cat cat1() {
        return new Cat("tomcat");
    }
    
	//此处表示如果容器中有cat1组件,则注入cat2,如果没有cat1组件,就不注入cat2
    @ConditionalOnBean(name = "cat1")
    @Bean
    public Cat cat2() {
        return new Cat("tomcat");
    }
}
```



## @Import

给配置类导入组件,值为一个class数组

```java
@Configuration
@Import({Cat.class})
public class Config01 {}
```



## @ImportResource

给配置类导入一个文件

```java
@Configuration
@ImportResource({"classpath:beans.xml")
public class Config01 {}
```









# 配置文件

springBoot的配置文件是resource目录下的以application命名的配置文件,这个配置文件可以是properties类型,也可以是yaml类型,也可以是yam类型

如果这三种类型的配置文件同时存在,name优先级properties>yml>yaml

yaml和yml的语法是一样的



## yaml/yam语法

例如有以下类

```java
@Component
@ConfigurationProperties(prefix = "class")
@Data
@ToString
public class Class {
    private String name;
    private Cat teacher;
    private Cat[] boys;
    private List<Cat> girls;
    private Map<String, Cat> score;
}
```

```java
@Component
@Data
@ToString
public class Cat {
    private String name;
    private String age;
}
```



现在我们使用yaml来给Class类注入属性,yaml使用缩进来表示层级关系

```yaml
#class对应prefix
class:
  #普通字面量属性,使用k: v的形式,注意:后面有个空格
  name: origin
  #对象属性,可以通过缩进加k: v的形式表示,也可以通过{k: v, K: v}的形式
  teacher:
    name: tomcat
    age: 2
  #数组或list属性(具有顺序的属性),使用-表示数组内每个元素, 也可以使用[]表示
  boys:
    - name: cat1
      age: 1
    - name: cat2
      age: 3
  girls: [{name: cat3, age: 3}, {name: cat4, age: 4}]
  #map,同对象属性,使用{k: v, K: v}或缩进加k: v
  score:
    math: {name: cat5, age: 5}
    english:
      name: cat6
      age: 6
```



注意在yaml中,写字符串的时候,如果加上`""`,其内的转义字符例如`\n`就能正常生效,但如果换成`''`或者不加引号,转义字符就会被再次转义,不能生效





## 读取配置文件值

### @ConfigurationProperties

该注解可以给类的属性注入配置文件中的指定的值

例如我们在配置文件中写入一个属性值

```yaml
cat: 
  name: tomcat;
  age: 2;
```

给容器中的Cat类加上@ConfigurationProperties注解,表示该类中的属性从配置文件中读取值并注入,其中的prefix和value属性互为别名,表示匹配配置文件中的某个前缀

```java
@Component
@ConfigurationProperties(prefix = "cat")
public class Cat {
    private String name;
    private Integer age;
    //......
}
```



### @Value

还有一种是在要注入的属性上标识@Value注解,并使用`${}`表达式来注入配置文件中的值

```java
@Value("${test01}")
private String test;
```

表达式中,注入内部属性使用`property.p1`的形式,注入数组内部值使用`array[index]`的形式

这种表达式同样适用于配置文件中属性的相互引用



### Environment对象

添加Environment对象能自动注入配置文件中的所有内容

```java
@Autowired
private Environment environment;
```

获取时,通过getProperty()方法获取

```java
environment.getProperty("spring.banner.location")
```





# web开发



## 静态资源

springBoot默认在resources目录下的以下目录存放静态资源

- `/static`
- `/public`
- `/resources`
- `/META-INF/resources`

也可以手动修改存放静态资源的位置

```yaml
spring: 
  web: 
    resources: 
      static-locations: [classpath:/mystaticResources]
```



访问静态资源时,可以直接使用`/资源名`的形式访问,因为对于任何一个请求,springMVC都会先在controller中匹配请求,如果controller中没有匹配到,再去查看静态资源文件夹下是否有该资源

我们也可以手动修改访问静态资源的匹配路径

```yaml
spring:
  mvc:
    static-path-pattern: /static/**
```





## 欢迎页和ico

在静态资源下放入`index.html`和`favicon.ico`来实现欢迎页功能和ico功能(注意这两个功能和spring-mvc-static-path-pattern冲突:expressionless:)





## restful

在配置文件中开启hidde-method以在表单中使用`_method`参数来发送delete和put请求

```yaml
spring:
  mvc:
    hiddenmethod:
      filter:
        enabled: true
```





## 矩阵参数

例如有如下映射路径, @MatrixVariable注解用于获取请求中的矩阵参数

```java
@GetMapping("/matrix/{path1}/{path2}")
@ResponseBody
public Map testMatrix(@MatrixVariable("m1") String m1, @MatrixVariable("m2") String m2,
                      @MatrixVariable("m3") Integer m3, @MatrixVariable("m4") Integer m4) {
    HashMap<String, Object> map = new HashMap<>();
    map.put("m1", m1);
    map.put("m2", m2);
    map.put("m3", m3);
    map.put("m4", m4);
    return map;
}
```



匹配该请求,其中m1,m2,m3,m4为矩阵参数

```
localhost:8080/matrix/p1;m1=你好;m2=世界;/p2;m3=30;m4=40
```



可以看出,矩阵参数必须跟在路径变量之后,矩阵参数之间用`;`分隔



# 第三方整合



## 测试

先引入spring的测试框架

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



在需要作为测试类的类上添加@SpringBootTest注解,就可以使用junit的测试框架了,但是要注意该测试类的包路径要和引导类(@SpringBootApplication)的包路径相同,不同的话需要另外配置一些东西

```java
@SpringBootTest
class Demo01ApplicationTests {

    @Test
    void contextLoads() {
    }
}
```



## 整合mybatis

