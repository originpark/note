# springmvc概述

mvc即model, view, control

- **Model** 模型层,指Javabean, 分为实体bean(User, Book)和业务bean(UserDao, UserService),用于处理数据
- **View** 视图层 指html或jsp页面,用于与用户交互
- **Control** 控制层,指servlet,用于接收请求和响应浏览器



Springmvc是spring的一个子项目,其本质是servlet,通过前端控制器DispatcherServlet对请求和响应进行统一处理



# idea创建maven的web项目

创建maven项目

在pom中设置打包方式为war表明该项目为web项目

在src\main下创建webapp目录

在项目结构的modules中给当前项目添加和WEB-INF和web.xml





# hello,springMVC

在springMVC中,前端发送的请求统一由一个名叫DispatcherServlet(前端控制器)的servlet处理

在web.xml中配置前端控制器dispatcherServlet

```xml
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置springMVC配置文件的名称和位置, 默认位于WEB-INF下, 在maven工程中,我们希望他在resources目录下-->
    <init-param>
        <!--这个参数名称是固定的-->
        <param-name>contextConfigLocation</param-name>
        <!--classpath:表示从类路径查找配置文件，maven工程中类路径的根路径为src/main/resources-->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--将前端控制器的启动时间提前到服务器启动时-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <!--/表示匹配所有的资源,除了.jsp-->
    <!--/*表示匹配所有的资源,包括.jsp-->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```





配置resouurces下的springMVC.xml,配置注解扫描,并加入thymeleaf需要的视图解析器,prefix和suffix决定了该解析器解析的资源地址为/WEB-INF/templates/*.html

```xml
<!--    配置注解扫描-->
<context:component-scan base-package="com.origin"></context:component-scan>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <!--        配置该视图解析器的优先级,表明可以有多个视图解析器-->
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">

                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>

                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8"/>
                </bean>
            </property>
        </bean>
    </property>
</bean>
```



被解析的index.html页面

```html
<!DOCTYPE html>
<!--xmlns:th="http://www.thymeleaf.org"是使用thymeleaf标签需要的命名空间-->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>index</h1>
</body>
</html>
```





设置控制器类,发送请求时,请求路径通过@RequestMapping匹配相应的方法,方法返回一个视图名称,该视图名称通过视图解析器解析加上前后缀,解析为具体的html页面并在前端显示

```java
@Controller
public class HalloController {

    //请求映射,value中为要匹配的请求地址
    @RequestMapping(value = "/")
    public String index() {
        //返回视图名称
        return "index";
    }
}
```





使用thymeleaf的a标签，如果直接写一个普通的href="/target.html", 则该href是交给浏览器解析的,浏览器会将/解析为http://ip:port/,而使用th里面的href就能自动给我们加上context path

```html
<a th:href="@{/target}">go to target.html</a>
```





## 总结流程

我们在web.xml中配置了一个springmvc封装好的servlet，叫做dispatcherServlet（前端控制器），并设置其url为/，表示匹配所有路径，同时给该servlet配置了一个springmvc的配置文件，在该配置文件中配置好了thymeleaf的视图解析器，当浏览器发送请求后，该请求被前端控制器匹配，前端控制器会读取springmvc的配置文件，通过注解扫描找到控制器类，根据控制器类中的requestMapping注解匹配到相应的方法，该方法就是处理请求的方法，处理请求的方法需要返回一个视图名称，thymeleaf的视图解析器给其加上前后缀，得到指定html地址，thymeleaf对视图进行渲染，最终转发到视图所对应的页面





# 匹配请求



## 概述

RequestMapping注解的功能是将注解标注的方法和一个请求路径关联起来，建立映射关系

一个请求路径只能关联一个方法



## 注解位置

RequestMapping注解可以加在方法上，还可以加在类上，表示设置一个父级匹配路径，例如

```java
@Controller
@RequestMapping("/test")
public class HalloController {

    //请求映射,value中为要匹配的请求地址
    @RequestMapping(value = "/")
    public String index() {
        //返回视图名称
        return "index";
    }

    @RequestMapping("/target")
    public String target() {
        return "target";
    }
}
```



这里给HalloController类设置了一个RequestMapping注解

此时index方法所映射的路径就是/test/

target方法所映射的路径就是/test/target





## 注解值

requestMapping注解的值都是用于设置匹配条件的,如果写了多个属性,则匹配时需要这些属性的条件都满足才会匹配成功

### value

```java
@AliasFor("path")
String[] value() default {};
```

value为一个String数组,数组内每个值都是一个请求地址,即value可以匹配一至多个请求地址

value是requestMapping必写的属性,其他属性都可以忽略





### method

```java
RequestMethod[] method() default {};
```

method为一个枚举数组,数组内每个值代表一个请求方式,例如get和post,如果不写method,则表明不以请求方式作为匹配条件,即所有请求方式都匹配

```java
@RequestMapping(
    value = {"/target"},
    method = {RequestMethod.GET}
)
```



springMVC提供了指定请求方式的requestMapping的派生注解

```java
@PostMapping("/test03")//匹配路径为/test03的post请求
@GetMapping("/test02")//匹配路径为/test03的get请求
```



请求方式还有put和delete,但是浏览器不支持这两种方式





### params

匹配请求参数,为一个String数组,只有当数组内每个值都匹配成功后才算匹配成功,下面是params值的几种写法,即可以使用!和=来进行一些逻辑匹配,例如!param表示参数不能有param

```java
params = {"param1", "!param2", "param3=value1", "param4!=value2", "param5"}
```



在html中设置有参数的请求,thymeleaf解析的请求中参数可以用括号括起来写,如下

```java
<a th:href="@{/test/target(param=123)}">go to target</a>
```





### headers

headers匹配的是请求头,为一个String数组,基本与params相同,只不过匹配的是请求头的键值对



## ant

springmvc支持在RequestMapping注解中使用ant形式的路径,类似于正则

?: 表示任意单个字符

*: 表示任意0个或多个字符

**: 表示任意一层或多层目录,只能使用`/**/xxx`的形式





## 占位符

以前我们在请求中添加参数的时候是`/test/testPlaceHolder?id=1`

在springmvc中我们可以写成这样的格式`/test/testPlaceHolder/1`



在控制器的requestMapping注解中

```java
//{}包起来的部分表示是一个参数,里面填参数的别名
@RequestMapping("/testPlaceHolder/{id}")
//@PathVariable注解意为路径参数,括号内填入参数别名,被修饰的形参id就会被注入相应的参数值
public String targetPlaceHolder(@PathVariable("id") Integer id) {
    System.out.println("得到的参数id:" + id);
    return "target";
}
```



注意:如果使用?和&来拼接参数,那么在匹配请求地址时,会将参数忽略匹配,例如/user可以匹配到/user?id=10

但是如果使用占位符的话,在匹配请求时参数也会是匹配的一部分,例如/user是无法匹配/user/10的

# 获取请求参数



## ServletAPI获取参数

可以直接在匹配到的方法中加入ServletAPI形参,前端控制器会自动帮我们传参

```java
@RequestMapping(value = "/")
public String index(HttpServletRequest request) {
    String id = request.getParameter("id");
    System.out.println("id = " + id);
    return "index";
}
```





## 控制器方法形参映射请求参数

可以直接以参数名命名,作为形参传入控制器方法中

```java
@RequestMapping(value = "/")
public String index(Integer id) {
    System.out.println("id = " + id);
    //返回视图名称
    return "index";
}
```

对于复选框,传入字符串或字符串数组都可以







### 以注解方式与形参形成映射关系



#### RequestParam与参数名形成映射

使用RequestParam注解来映射路径中的参数名和形参中的参数名

```java
@RequestMapping(value = "/test")
public String index(@RequestParam("name") String userName,
                    @RequestParam("password") String password) {
    System.out.println("name = " + userName + ", password = " + password);
    return "index";
}
```



RequestParam注解必填属性为value,即要映射的参数名



RequestParam注解另一个required属性,默认值为true,代表该属性必须存在,即传参的时候该注解所映射的参数必须存在,否则就会报错,可以手动令required为false,这样对应的参数就可传可不传了

```java
boolean required() default true;
```



RequestParam注解还有一个defaultValue属性,表示在没有传递该参数或传递过来一个空串的时候,给该参数赋的默认值





#### RequestHeader与请求头形成映射

用法与RequestParam相同,也有value, required, defaultValue三个属性

```java
@RequestMapping(value = "/test")
public String index(@RequestParam("name") String userName,
                    @RequestParam("password") String password,
                    @RequestHeader("host") String host) {
    System.out.println("name = " + userName + ", password = " + password + ", host = " + host);
    return "index";
}
```





#### @cookieValue与cookie形成映射

用法同上,也有value, required, defaultValue三个属性

例如此处获取session用于唯一标识客户端的JSESSIONID

```java
@RequestMapping("/testSession")
public String testSession(@CookieValue("JSESSIONID") String JSESSIONID) {
    System.out.println(JSESSIONID);
    return "index";
}
```



#### @RequestAttribute

给参数标注RequestAttribute注解可以给该参数注入request域中的属性





## 将请求参数赋值给bean对象实例

当我们在匹配器方法的形参中设置一个bean对象时,和bean对象的属性名相同的请求参数会自动注入给该属性

```java
@RequestMapping(value = "/test")
//这里的User有id和name两个属性,如果请求参数中有id或者name就会自动注入给这个user的相应属性
public String index(User user) {
    System.out.println(user);
    return "index";
}
```





## 乱码问题

get请求乱码通过在Tomcat的server.xml文件中配置



对于post请求乱码,需要我们通过request.setEncoding方法设置编码来解决,但是这个方法只有在servlet第一次获取参数之前调用才有效,而在我们控制器的方法被调用时,DispatcherServlet实际上已经获取过参数了,因此在控制器方法中设置编码是无效的

因此,考虑使用filter在DispathcerServlet获取参数之前设置编码,springmvc帮我们封装了这一步骤,我们只需要在web.xml注册这一功能即可



在web.xml中配置springmvc为我们封装好的characterEncodingFilter

```xml
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!--初始化CharacterEncodingFilter的encoding属性,该属性就是我们要设置的编码-->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <!--初始化forceResponseEncoding属性,是一个boolean类型属性,为true时表示同时设置响应报文的编码-->
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```







# 域对象共享数据

三个域对象request, session, context(ServletContext)

还有一个jsp特有的pageContext





## 向request中设置数据



### ServletAPI

即通过HttpServletRequest等对象来设置共享数据

```java
@RequestMapping("testScopeByServletAPI")
public String testScopeByServletAPI(HttpServletRequest request) {
    request.setAttribute("key01", "value01");
    return "result";
}
```



在html中显示域对象的数据

```html
<p th:text="${'key1'}"></p>
```

若要获取request域数据,直接写key即可,如果要获取session或是context中的数据,就要加上sessionScope.key





### ModelAndView

modelAndView包含两个功能,一是给request对象设置属性,二是设置视图,在控制器的方法中返回一个ModelAndView对象来实现设置request域属性和转到执行页面的功能

```java
@RequestMapping("testModelAndView")
public ModelAndView testModelAndView() {
    ModelAndView mav = new ModelAndView();
    //给request设置属性
    mav.addObject("key02", "value02");
    //设置视图名称
    mav.setViewName("result");
    return mav;
}
```



注意:

控制器中的所有方法都是由DispatcherServlet调用的,DispatcherServlet调用这些方法后,都会返回一个ModelandView对象,即只要调用控制器方法,最终在DispatcherServlet里面都会返回一个ModelAndView对象

ModelAndView对象中有两个属性,一个是Object类型的view,表示视图名称,一个是Map的间接实现类Model,用以存放一个request域中的数据





### Model

Model对象可以给request设置属性,在形参中传入model对象

```java
@RequestMapping("testModel")
public String testModel(Model model) {
    model.addAttribute("key03", "value03");
    return "result";
}
```





### Map

向控制器方法形参中传入一个map,然后给该map添加数据,也能实现给request设置属性

```java
@RequestMapping("testMap")
public String testMap(Map<String, Object> map) {
    map.put("key04", "value04");
    return "result";
}
```





### ModelMap

```java
@RequestMapping("testModelMap")
public String testModelMap(ModelMap modelMap) {
    modelMap.addAttribute("key05", "value05");
    return "result";
}
```



注意:

使用Model, Map, ModelMap方式时,其运行类型都是BindingAwareModelMap,该类间接地继承或实现了这三个东西





## 向session中设置数据

直接使用ServletAPI,或者在参数中传入一个HttpSession





## 向application中设置数据

application即servletContext对象,可以通过session.getServletContext()方法获取





# springMVC视图

springMVC中有一个View接口,默认有两个实现类

- InternalResourceView 转发视图
- RedirectView 重定向视图

当我们使用了thymeleaf框架时,就多了一种实现类

- ThymeleafView





## 三种视图

在DispatcherServlet中,通过view对象来跳转至不同的资源

从前我们在控制器方法中返回一个视图名称,例如"index",该视图名称会在dispatcherServlet中被封装成一个ThymeleafView对象,该ThymeleafView对象通过其视图解析器解析index.html并显示在浏览器页面上

当我们将返回的视图名称改为有前缀的形式,例如"forward:/index",此时该视图名称在dispatcherServlet中会被封装成一个InternalResourceView对象,该InternalResourceView对象会将前缀forward:去除,然后通过请求转发的方式将请求转发至"/index",于是该请求路径"/index"再次被dispatcherServlet处理,此时没有了前缀,于是封装为ThymeleafView对象,通过视图解析器解析index.html页面并显示

同理,如果返回的视图名称是"redirect:/index",那么将会在DispatcherServlet中被封装为RedirectView对象,将前缀去除后,以请求重定向的方式请求"/index",然后再次被封装为ThymeleafView,通过视图解析器解析index.html并显示

简单来说就是返回有前缀的视图名称时,该视图名称会被DispatcherServlet处理两次,第一次进行请求的转发或重定向处理,第二次进行页面的解析显示





## 视图控制器

如果某个控制器方法中只有一句return,即没有其他的业务处理,那么就可以在springMVC.xml中使用标签来实现页面的跳转

path表示要匹配的路径,view-name表示返回的视图名称

```xml
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
```



如果使用了这种方法,那么默认所有的控制器方法都会失效,在springMVC.xml中使用下面的标签来解决这一问题

```xml
<mvc:annotation-driven/>
```







# RESTFul



## RESTFul概述

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移。

概念啥的就不说了,大概的思想就是:例如我们需要对User类进行的增删改查操作,那我们就将请求路径统一写为/user,有参数就在后面加参数,然后使用不同的请求方式来区别不同的操作,查询操作用get请求,添加操作用post请求,修改操作用put请求,删除操作用delete请求

这样一来,好处是对user的所有操作在请求路径上都表现为/user,省去了各种操作的命名问题





## 发送put和delete请求

现在有一个问题,即浏览器只能发起get和post请求,无法发送delete和put请求,因此springMVC为我们封装了一个filter来解决这个问题,即HiddenHttpMethodFilter

下面看这个filter在执行doFilter方法之前都做了些什么

```java
//HiddenHttpMethodFilter类的doFilterInternal方法
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
      throws ServletException, IOException {
   //将当前请求的request对象保存在requestToUse属性中
   HttpServletRequest requestToUse = request;
	//判断当前请求的request对象的请求方式为是否为post(后面那个判断一定返回true,不用管)
   if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {
       //public static final String DEFAULT_METHOD_PARAM = "_method";
       //这里即获取当前请求的一个叫"_method"的参数
      String paramValue = request.getParameter(this.methodParam);
      if (StringUtils.hasLength(paramValue)) {
          //如果这个参数存在,则将其转为全大写
         String method = paramValue.toUpperCase(Locale.ENGLISH);
          //判断转为大写后的这个参数是否是PUT,DELETE,PATCH中的一个
         if (ALLOWED_METHODS.contains(method)) {
             //以当前请求和method生成一个新的HttpServletRequest对象保存在requestToUse中
             //这个HttpMethodRequestWrapper是HttpServletRequest的间接实现类
            requestToUse = new HttpMethodRequestWrapper(request, method);
         }
      }
   }
	//到这里,如果传进来的请求的请求方式为post,且有一个"_method"参数的话,那么当前请求的请求方法就
    //会被修改为"_method的值"
   filterChain.doFilter(requestToUse, response);
}
```



分析源码可以看出,只要在浏览器发送一个含有`_method`参数的post请求,那么这个请求在发送到服务器时,经过HiddenHttpMethodFilter的过滤会转为其他类型的请求,请求类型由`_method`的值决定,这样我们就可以发送put和delete请求了



因此发送put请求,首先在web.xml中配置HiddenHttpMethodFilter,其次发送一个名为"_method", 值为"put"的post请求即可 



发送delete请求麻烦一点,因为通常我们的删除操作都是使用超链接去完成的,但是超链接默认只能发送get请求,不能发送post请求

解决办法是,额外创建一个有"_method"参数的form表单,给超链接绑定一个单击事件,当点击超链接时,先阻止该超链接的默认行为,然后将其href属性赋值给form表单的action属性,然后使form表单提交,即通过超链接来控制form表单



给超链接绑定单击事件

```html
<script type="text/javascript">
    window.onload = function () {
        let deleteLinks = document.querySelectorAll("#delete");
        for (let deleteLink of deleteLinks) {
            deleteLink.addEventListener("click", function (event) {
                event.preventDefault();
                let form = document.querySelector("#deleteForm");
                form.action = deleteLink.href;
                form.submit();
            })
        }
    }
</script>
```



超链接控制的form表单

```html
<form id="deleteForm" method="post">
    <input type="hidden" name="_method" value="delete">
</form>
```







# HttpMessageConverter

HttpMessageConverter,译为报文信息转换器,指将请求报文转换为java对象,或将java对象转换为响应报文

HttpMessageConverter提供了两个注解和两个类型,@RequestBody, @ResponseBody, RequestEntity, ResponseEntity



## @RequestBody

该注解用于标记控制器方法中的形参,表示该参数为post请求的请求体(get请求没有请求体)

```java
@RequestMapping("/test01")
public String test01(@RequestBody String requestBody) {
    System.out.println(requestBody);//name1=value1&name2=value2
    return "index";
}
```





## RequestEntity

entity就是实体的意思,RequestEntity是一个类,直接在控制器形参中传入该类,就可以获取该对象

```java
@RequestMapping("/test02")
public String test02(RequestEntity<String> entity) {
    System.out.println(entity.getHeaders());//获取请求头
    System.out.println(entity.getBody());//获取请求体
    return "index";
}
```





## @ResponseBody

给控制器方法加上@ResponseBody注解之后,该方法的返回值就会自动转为响应报文的响应体

注意!!!如果返回的是我们自定义的实体对象,一定要确保该类有getter和setter方法

```java
@RequestMapping("/test03")
@ResponseBody
public String test03() {
    return "<h1>hello, world.</h1>";
}
```



### 返回json

不能直接在响应体中写入java对象,因为浏览器并不能识别java对象数据,因此如果想要响应java对象给浏览器,就需要先将java对象转为json字符串,然后再响应给浏览器



首先引入jackson依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```



其次开启mvc注解驱动

```xml
<mvc:annotation-driven/>
```



此时在有@ResponseBody注解的控制器方法中就可以直接返回java对象给响应体了,该依赖会自动帮我们将java对象转为json字符串

### @RestController

@RestController注解用于标注控制器类,作用相当于给该类添加@Controller,并且给该类的所有方法添加@ResponseBody





## ResponseEntity

ResponseEntity是一个类,通常作为控制器方法的返回值返回





# 文件下载与上传



## 文件下载

通过返回一个ResponseEntity对象的方式将文件响应给浏览器并下载

```java
@RequestMapping("/test05")
public ResponseEntity test05(HttpSession session) throws IOException {
    //通过传入的session获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //通过servletContext对象获取目标文件的真实路径(maven项目真实路径为target目录下)
    String realPath = servletContext.getRealPath("/static/001.jpg");
    //获取目标文件的输入流
    FileInputStream inputStream = new FileInputStream(realPath);
    //available方法用于获取输入流的可读取的字节数,也就是该文件的字节数
    byte[] bytes = new byte[inputStream.available()];
    //将文件数据输入到字节数组中
    inputStream.read(bytes);
    //创建请求头对象,并添加文件下载相关的请求头
    MultiValueMap<String, String> headers = new HttpHeaders();
    headers.add("Content-Disposition", "attachment;filename=001.jpg");
    //创建响应状态码对象
    HttpStatus status = HttpStatus.OK;
    //创建ResponseEntity对象,传入文件字节数组,响应头,响应状态码
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, status);
    inputStream.close();
    //返回responseEntity对象
    return responseEntity;
}
```





## 文件上传

首先添加依赖

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```



在springMVC.xml中配置文件上传解析器,该解析器的功能是将上传的文件封装为MultiPartFile对象

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```



在html页面中设置文件上传表单,必须保证

1. enctype="multipart/form-data"
2. method="post"
3. 文件input标签

```java
<form enctype="multipart/form-data" th:action="@{/test06}" method="post">
        上传头像:<input type="file" name="photo" id="">
        <input type="submit" value="submit">
    </form>
```



控制器中的文件上传方法,形参中传入一个MultipartFile对象,springMVC已经通过文件上传解析器帮我们封装好了这个对象,注意形参名要与input标签中的name值相同

```java
@RequestMapping("/test06")
public String test06(MultipartFile photo, HttpSession session) throws IOException {
    //获取文件的原始文件名
    String filename = photo.getOriginalFilename();
    //处理文件重名问题
    String suffix = filename.substring(filename.lastIndexOf("."));
    filename =  UUID.randomUUID().toString() + suffix;
    //通过session获取servletContext对象
    ServletContext servletContext = session.getServletContext();
    //通过servletContext对象得到文件保存的目录,但是该目录当前可能不存在
    String savePath = servletContext.getRealPath("/photo");
    //如果该目录不存在,就创建一个新目录
    File file = new File(savePath);
    if (!file.exists()) {
        file.mkdir();
    }
    //得到文件存储的最终路径
    String finalPath = savePath + "/" + filename;
    //transfoerTO方法用于将文件转移到指定文件中,其底层就是做了输入和输出的工作
    photo.transferTo(new File(finalPath));
    return "index";
}
```





# 拦截器

过滤器作用于浏览器的请求交给前端控制器之前,拦截器作用于控制器的方法执行的前后





## 拦截器使用

设置一个类实现HandlerInterceptor接口,重写其中的三个方法,这三个方法分别执行与控制器方法执行之前,控制器方法执行之后,视图渲染完成之后

```java
public class MyIntercept implements HandlerInterceptor {
    //控制器方法执行之前执行,返回true则放行,返回false则不会调用控制器方法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("pre");
        return true;
    }
	//控制器方法执行完之后调用
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("post");
    }
	//视图渲染完成之后调用
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("after");
    }
}
```



在springMVC.xml中配置拦截器,直接在ioc容器中新创建一个拦截器bean对象,这样拦截的是所有页面

```xml
<mvc:interceptors>
    <bean class="com.origin.intercept.MyIntercept"></bean>
</mvc:interceptors>
```



还可以引用某个已存在的bean,这样拦截的也是所有的页面

```xml
<mvc:interceptors>
	<ref bean="myIntercept"></ref>
<mvc:interceptors>
```



使用下面的方式来指定拦截页面

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <!--/*表示拦截/下的一层目录,/**表示拦截/下的所有路径-->
        <mvc:mapping path="/**"/>
        <!--exclude表示不拦截该页面-->
        <mvc:exclude-mapping path="/"/>
        <!--配置拦截器类-->
        <ref bean="myIntercept"></ref>
    </mvc:interceptor>
</mvc:interceptors>
```





## 多个拦截器执行机制

例如有5个拦截器,其配置顺序依次是i1, i2, i3, i4, i5

preHandle的执行顺序与配置顺序一致

postHandle和afterCompletion执行顺序与配置顺序相反

当有拦截器的pre方法返回false时,例如执行i3的pre方法返回了false,此时接下来的i4和i5的pre方法都不会执行,所有的post方法也都不会执行,对于afterCompletion方法,只会执行i1和i2的afterCompletion方法





# 异常处理器



## 基于配置的异常处理

springMVC提供了一个用于处理控制器方法中出现的异常的接口HandlerExceptionResolver

HandlerExceptionResolver接口的实现类有：DefaultHandlerExceptionResolver和SimpleMappingExceptionResolver

其中SimpleMappingExceptionResolver为自定义异常处理器,可以在控制器方法中出现异常时跳转到指定页面

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <!--给SimpleMappingExceptionResolver类注入一个exceptionMappings属性-->
    <property name="exceptionMappings">
        <props>
            <!--exceptionMappings为一个properties,prop的key属性为要捕获的异常,prop标签的值为要跳转到的视图-->
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--exceptionAttribute-->属性用于保存错误信息,以value作为key保存在请求域中
    <property name="exceptionAttribute" value="message"></property>
</bean>
```





## 基于注解的异常处理

```java
//@ControllerAdvice将当前类标识为异常处理的组件
@ControllerAdvice
public class ExceptionController {

    //@ExceptionHandler用于设置所标识方法处理的异常
    @ExceptionHandler(ArithmeticException.class)
    //ex表示当前请求处理中出现的异常对象
    public String handleArithmeticException(Exception ex, Model model){
        model.addAttribute("ex", ex);
        return "error";
    }
}
```





# 注解配置springMVC

首先写一个类继承AbstractAnnotationConfigDispatcherServletInitializer类,用以代替web.xml文件

```java
/web工程初始化类,用于代替web.xml
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {

    //指定spring的配置类
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    //指定springMVC的配置类
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    //指定DispatcherServlet的url-pattern
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    //注册过滤器
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("utf-8");
        characterEncodingFilter.setForceResponseEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter, hiddenHttpMethodFilter};
    }
}

```



spring的配置类,mvc阶段暂时用不到,等到整合ssm的时候会用到

```java
@Configuration
public class SpringConfig {
}
```



springMVC的配置类,用于替代springMVC.xml

```java
//将当前类标识为一个配置类
@Configuration
//配置注解扫描
@ComponentScan(basePackages = {"com.origin"})
//配置注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    //1. 配置视图解析器
    //(1)配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(
                webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }

    //(2)生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //(3)生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }

    //2. 配置默认的servlet来处理静态资源,需要本类实现WebMvcConfigurer接口,重写下列方法
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //3. 配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        IndexInterceptor indexInterceptor = new IndexInterceptor();
        registry.addInterceptor(indexInterceptor).addPathPatterns("/**");
    }

    //4. 配置视图控制器
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }

    //5. 配置文件上传解析器
    @Bean
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    //6. 配置异常映射
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties prop = new Properties();
        prop.setProperty("java.lang.ArithmeticException", "error");
        //设置异常映射
        exceptionResolver.setExceptionMappings(prop);
        //设置共享异常信息的键
        exceptionResolver.setExceptionAttribute("ex");
        resolvers.add(exceptionResolver);
    }
}

```

