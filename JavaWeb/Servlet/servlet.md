# Servlet概述

servlet是javaEE规范之一，就是一个**接口**

javaWeb的三大组件：Servlet程序，Filter过滤器，Listener监听器

Servlet是运行在服务器上的java小程序，它可以**接收客户端发送过来的请求，并响应给客户端**





# 实现Servlet程序



先编写一个类实现Servlet接口，注意需要引入tomcat的lib目录下的Servlet-api.jar

```java
import javax.servlet.*;
public class Servlet_ implements Servlet {}
```



实现Servlet后需要实现其接口方法，其中`Servlet`方法用来处理请求，并相应数据

```java
@Override
public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
    System.out.println("hello,servlet");
}
```





在web.xml中配置Servlet程序的访问地址

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--    servlet标签给Tomcat服务器配置我们写好的servlet类的程序-->
    <servlet>
<!--        servlet-name标签给servlet程序取一个别名，通常使用类名-->
        <servlet-name>Servlet_</servlet-name>
<!--        servlet-class是servlet的全类名-->
        <servlet-class>com.origin.Servlet.Servlet_</servlet-class>
    </servlet>
<!--    servlet-mapping标签给servlet程序配置访问地址-->
    <servlet-mapping>
<!--        该servlet-name用以表明要给哪个servlet程序配置地址-->
        <servlet-name>Servlet_</servlet-name>
<!--        url-pattern标签用以配置访问地址
            '/'在解析时表示http://ip:port/工程路径/
            因此/hello表示http://ip:port/工程路径/hello
            这里的工程路径即该项目启动时进入的路径-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```



配置完成后，就可以通过配置好的地址访问servlet程序，例如此处使用http://locathost:8080/servlet01/hello来访问servlet程序

其原理是，http://locathost:8080能过连接到指定主机的指定端口，即服务器的8080端口上的tomcat服务，/servlet 为工程路径，指定了访问哪个工程，/hello 为资源路径，资源路径通过servlet-mapping中的url-pattern定位到指定servlet，查找到servlet-name，再通过servlet-name查找到servlet的全类名servlet-class，再执行该servlet类中的service方法







# Servlet的生命周期

一个完整的servlet接口实现类

```java
public class Servlet_ implements Servlet {

    public Servlet_() {
        System.out.println("constructor");
    }
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init()");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    //service方法是专门用来处理请求和响应的
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("hello,servlet");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("destroy()");
    }
}
```





其中，构造器方法和初始化方法只在第一次访问servlet时调用，即只创建一个servlet对象实例

接下来每次访问servlet，就会调用一次servlce方法，当停止项目时，调用destroy方法





# 继承HttpServlet实现Servlet程序

实际开发中，都是使用继承HttpServlet类的方式来实现Servlet程序

可以重写HttpServlet的doGet和doPost方法

```java
public class httpServlet_ extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //super.doGet(req, resp);
        System.out.println("doGet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //super.doPost(req, resp);
        System.out.println("doPost");
    }
}

```

然后与之前相同，在web.xml中给servlet程序配置地址即可





# Servlet类继承体系

Servlet全类名javax.Servlet，该接口定义Servlet的访问规范，即定义接口方法

```java
public interface Servlet {
    void init(ServletConfig var1) throws ServletException;
    ServletConfig getServletConfig();
    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
    String getServletInfo();
    void destroy();
}
```



GenericServlet类实现了Servlet接口，做了很多空实现，并持有一个ServletConfig引用

```java
public abstract class GenericServlet implements Servlet, ServletConfig, Serializable {
    private transient ServletConfig config;
}
```





HttpServlet实现了service方法，并实现了请求的分发处理，即通过getMethod方法确定时调用doGet方法还是doPost方法

```java
public abstract class HttpServlet extends GenericServlet {
    String method = req.getMethod();
    //......
}
```



我们自定义的Servlet通常继承HttpServlet，并重写其中的doGet和doPost方法





# ServletConfig类

ServletConfig类即Servlet的配置信息类

```java
public interface ServletConfig {
    String getServletName();

    ServletContext getServletContext();

    String getInitParameter(String var1);

    Enumeration<String> getInitParameterNames();
}
```



每个servlet对象都有一个servletConfig属性`config`，该属性会传入init方法，也可以通过`getServletConfig`方法获取

```java
ServletConfig servletConfig = getServletConfig();
```



可以看出ServletConfig的作用

1. 可以获取servlet的别名servlet-name的值
2. 获取初始化参数init-param
3. 获取`ServletContext`对象



在servlet程序的初始化方法传入了一个servletConfig对象，利用该对象可以：

获取servlet-name值

```java
 String servletName = config.getServletName();
```



获取web.xml中的初始化参数init-param

```xml
<!--xml文件中配置初始化参数-->
<servlet>
    <servlet-name>servletTest</servlet-name>
    <servlet-class>com.origin.Servlet.ServletTest</servlet-class>
    <init-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
    </init-param>
</servlet>
```



```java
//通过param-name获取param-value
String username = config.getInitParameter("username");
```





获取`ServletContext`对象

```java
ServletContext servletContext = config.getServletContext();
```



注意，servletConfig对象是在GenericServlet的init方法中赋值的

```java
//GenericServlet中的init方法
public void init(ServletConfig config) throws ServletException {
    this.config = config;
    this.init();
}
```



因此，当我们重写了init方法，同时又想使用servletConfig对象时，就需要在重写的init方法中先调用父级的init方法完成servletConfig对象的初始化

```java
//在我们的servlet类中重写init方法
@Override
public void init(ServletConfig config) throws ServletException {
    super.init(config);
    //如果不调用super.init(config)，那么这里的servletConfig就是null
    ServletConfig servletConfig = getServletConfig();
}
```







# ServletContext类

ServletContext是一个接口，一个web工程持有一个servletContext对象

```java
public interface ServletContext {}
```



**作用**

- 获取web.xml中配置的上下文参数context-param

  在web.xml中配置context-param

  ```xml
  <!--    context-param是上下文参数，属于整个工程-->
  <context-param>
      <param-name>username</param-name>
      <param-value>context</param-value>
  </context-param>
  ```

  获取context-param，通过ServletConfig对象获取ServletContext对象，再通过context对象获取context-param

  ````java
  ServletContext servletContext = getServletConfig().getServletContext();
  String username = servletContext.getInitParameter("username");
  ````

  

- 获取当前的工程路径

  ```java
  String contextPath = servletContext.getContextPath();
  ```

  

- 获取工程部署后在服务器磁盘上的绝对路径

  idea会将我们的web工程映射到项目的\out\artifacts\模块名_war_exploded路径下，此路径即对应着项目中的web目录

  `getRealPath`中传递空串即获取web目录所对应的映射目录

  例如此处为D:\idea_java_project\Origin\out\artifacts\Servlet__war_exploded\

  也可以传入指定路径，例如此处获取web目录下的css/index.css,

  获取到的地址为D:\idea_java_project\Origin\out\artifacts\Servlet__war_exploded\css\index.css

  ```java
  String realPath = servletContext.getRealPath("");
  String realPath = servletContext.getRealPath("/css/index.css");
  ```

  

- 像Map一样存取数据

  GenericServlet对象有一个获取ServletContext对象的方法

  ```java
  public ServletContext getServletContext() {
      return this.getServletConfig().getServletContext();
  }
  ```

  因此可以直接调用该方法来获取ServletContext对象

  ```java
  ServletContext servletContext = getServletContext();
  ```

​		使用context对象设置属性

```java
servletContext.setAttribute("key", "value");
Object key = servletContext.getAttribute("key");
```





# GET请求HTTP协议

客户端给服务器发送数据叫请求，服务器给客户端发送数据叫响应

请求分为GET请求和POST请求

大部分请求都是GET请求，method=post的form是POST请求，在浏览器中访问某个地址是GET请求



Get请求的HTTP协议分为：请求行，请求头

请求行中包含请求方式（GET），请求的资源路径，请求的版本号

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220408093406254.png" alt="image-20220408093406254" style="zoom:67%; margin-left:0;" />

请求头由key：value组成

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220408093508163.png" alt="image-20220408093508163" style="zoom:67%;margin-left:0;" />



请求头中的几个常见key：

- Accept 告诉服务器，客户端可以接收的数据类型
- User-Agent 用户代理，即浏览器，这里为浏览器信息
- Host 请求的服务器的端口号
- Connection: keep-alive 告诉服务器回传数据时不要马上关闭连接





# POST请求HTTP协议

POST请求HTTP协议的结构为请求行，请求头，请求体

请求行与请求头部分与GET请求相同

请求体是发送给服务器的数据





# 响应HTTP协议

HTTP协议的响应报文由响应行，响应头，响应体构成

响应行包含响应的协议的版本号，响应状态码，响应状态描述符

响应头由若干个key-value构成

响应体是回传给客户端的数据



常见响应头：

- Servlet 服务器信息
- Content-Type 响应体的数据类型
- Content-length 响应体的长度
- Date 请求响应的时间



常见响应状态码：

-  200 表示请求成功
- 302 表示请求重定向
- 404 表示服务器收到了请求，但是请求的数据不存在
- 500 服务器收到请求，但是服务器内部错误，即服务器的代码有bug





# HttpServletRequset类

每次只要有请求进入Tomcat服务器，服务器就会解析请求过来的THHP协议，并封装到一个`HttpServletRequest`类的对象中

在doGet和doPost方法中会传入request对象供我们使用

通过HttpServletRequest类可以获取到HTTP请求的所有信息



**常用方法**



获取路径

```java
// 获取请求的资源路径servlet01/hello(工程名/资源名)
String requestURI = request.getRequestURI();
// 获取请求的统一资源定位符，即绝对路径http://localhost:8080/servlet01/hello
StringBuffer requestURL = request.getRequestURL();
```



获取客户端ip

```java
String remoteHost = request.getRemoteHost();
```



获取某个指定的请求头

```java
String header = request.getHeader("User-Agent");
```



获取请求中的参数，例如获取form中的name为username的input标签的值

```java
String parameter = request.getParameter("username");
```



获取请求中的参数（多个值），例如获取form中的某个多选框hobby的所有被选中的值

```java
String[] parameterValues = request.getParameterValues("hobby");
```



注意点：当使用POST请求获取参数，并且该参数为中文时，就会出现乱码

解决方法是设置请求体的字符集，该方法必须在获取参数之前调用才有效

```java
req.setCharacterEncoding("utf-8");
```



## 请求转发

服务器收到请求后，从一个资源跳转到另一个资源的操作叫做请求转发

例如，form发送post请求到Servlet01，在Servlet01中将请求转发到Servlet02中

```java
// Servlet01中的doPost方法
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //在Servlet01中获取参数
    String username = req.getParameter("username");
    System.out.println("username in Servlet01 = " + username);
    //给req对象设置Attribute
    //可以简单理解Attribute是服务器内部设置的参数，Parameter是前端页面传给服务器的参数
    req.setAttribute("key1", "ok in Servlet01");
    //设置要转发到的Servlet或者其他资源
    RequestDispatcher requestDispatcher = req.getRequestDispatcher("/fromAdd");
    //请求转发，并将Servlet01的request和resp传给要转发到的Servlet
    requestDispatcher.forward(req, resp);
}
```



```java
//Servlet02中的doPost方法
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //这里的req就是Servlet01中的request对象，并没有再创建一个request对象
    String username = req.getParameter("username");
    System.out.println("username in Servlet02 = " + username);
    //得到req的Attribute
    Object key1 = req.getAttribute("key1");
    System.out.println("Servlet02 receive the massage in Servlet01: " + key1);
    //在Servlet02中继续处理请求
    System.out.println("Servlet02 do something......");
}
```







**特点**

1. 请求转发虽然经过了多个Servlet，但是地址不会变，仍然是第一个Servlet的地址，属于一次请求
2. 转发中，多个Servlet共享一个Request对象
3. 转发到的路径只能是工程下的路径，因此可以转发至WEB-INF目录下，但是不能转发到工程外







# base标签&路径

使用请求转发跳转页面时，跳转到的地址不是页面的真实地址，而是进行请求转发的Servlet的地址，此时如果在跳转到的页面使用相对地址跳转到其他地址的话，就会出现地址解析错误

base标签的作用是，设置当前页面的所有相对路径工作时，参照哪个页面来进行跳转，base标签**设置在head标签中**，标签href中的test.html可以省略

```html
<base href=http://localhost:8080/Servlet02_war_exploded/test.html">
```



**JavaWeb中的相对路径**

`.` 表示当前目录

`../` 表示上一级目录

`资源名` 表示当前目录，资源名



**'/'的含义**

如果被浏览器解析，得到的地址是 http://ip:port/

如果被服务器解析，得到的地址是 http://ip:port/工程路径

有一种特殊情况，即`response.sendRedict("/");` 表示把"/"发送给浏览器解析，得到http://ip:port/





# HttpServletResponse

与`HttpServletRequest`相同，每次只要有请求，服务器就会创建一个response对象实例，通过response对象来设置响应给客户端的信息



## 响应流

response类有2个响应流，分别是字节流和字符流，通过getOutputSrream和getWriter获取

这2个流只能使用一个，不能同时使用这2个流



**向客户端回送数据**

使用字符流向客服端传送数据

```java
PrintWriter writer = resp.getWriter();
writer.write("response content");
```

此时 ”response content“ 就会显示在页面上



还需要解决中文乱码问题，有两种方式

```java
(1)
//先将服务器的字符集设置为utf-8
resp.setCharacterEncoding("utf-8");
//再给响应添加一个响应头content-type，设置浏览器的字符集
resp.setHeader("content-type", "text/html charset=utf-8");
```



```java
(2) 同时设置服务器和浏览器的charset，此方法一定要再获取流对象之前调用
resp.setContentType("text/html; charset=utf-8");
```



## 请求重定向

指用户访问的某些Servlet已经过时，因此需要在该过时的servlet中进行请求重定向，使用户访问另一个指定的程序

```java
//在过时的servlet进行请求重定向
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //设置响应状态码，302代表请求重定向
    resp.setStatus(302);
    //添加响应头Location，即请求重定向的地址
    resp.setHeader("Location", "http://localhost:8080//Servlet02_war_exploded/test");
}
```



上述代码也可以直接通过sendRedirect方法一步到位

```java
resp.sendRedirect("http://localhost:8080/Servlet02_war_exploded/test");
```

注意请求重定向时，url最好写绝对路径，可以使用req.getContextPath() + "/servlet"的方式



**特点**

- 浏览器地址栏会发生变化
- 是两次请求
- 2次请求创建了2个resquest对象，因此不共享resquest
- 不能重定向到WEB-INF下的资源，可以重定向到项目之外的路径
