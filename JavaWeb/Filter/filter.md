# filter概述

filter，翻译为过滤器，是javaEE的三大组件之一（servlet，listener，filter），是一个接口

filter的作用有拦截请求，例如有一些页面我们只希望登录后的用户才能访问，没登陆的用户访问该页面会跳转至登录页面，就可以使用filter



先编写一个类实现filter接口（javax.servlet.Filter），并重写其三个方法

其中，doFilter方法就是专门用来请求拦截的，我们可以在这里实现我们的拦截业务

```java
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) servletRequest;
        if (!true) {//不满足访问条件，跳转至其他页面
            req.getRequestDispatcher("/pages/user/login.jsp").forward(req, servletResponse);
        } else {//满足条件，调用FilterChain对象的doFilter方法，使请求正常进行
            filterChain.doFilter(servletRequest, servletResponse);
        }
    }

    @Override
    public void destroy() {

    }
}
```



然后，在web.xml中配置该filter

```xml
<filter>
    <filter-name>MyFilter</filter-name>
    <filter-class>com.origin.filter.MyFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <!--这里的url-pattern表示要拦截的页面，可以有多个url-pattern-->
    <url-pattern>/pages/*</url-pattern>
</filter-mapping>
```



# filter的生命周期

对应四个方法，构造方法，init方法，doFilter方法，destroy方法

web工程启动时，调用构造方法和init方法

每次拦截到请求时，调用doFilter方法

停止web工程时，调用destroy方法，同时filter销毁



# filterConfig类

filter的配置文件类，每次创建filter的时候，同时会创建一个filterConfig类

使用filterConfig类可以获取filter的配置信息：

- `getFilterName` 获取filter的别名filter-name 
- `getInitParameter`  获取在web.xml中filter标签中配置的init-param 初始化参数
- `getServletContext` 获取servletContext对象





# FilterChain

chain翻译为链条的意思，FilterChain即过滤器链的意思

当一个页面被多个filter拦截的时候，就需要规定这些filter的执行顺序



例如有filter1和filter2都拦截某个页面

```java
public class Filter1 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("pre code1");
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("post code1");
    }
}
```



```java
public class Filter2 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("pre code2");
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("post code2");
}
```



此时会先根据这两个filter在web.xml中配置的先后顺序来决定先进入哪个filter，如果filter1配置信息在前，则会先进入filter1的doFilter方法，打印pre code1，然后执行filterChain.doFilter方法，此时chain会判断是否有下一个filter，如果有，就直接进入，因此此时会进入filter2的doFilter方法，打印pre code2，然后执行filterChain.doFilter方法，此时没有下一个拦截该页面的filter了，因此会进入请求页面，然后一路返回，先打印post code2，再打印post code1



**注意**

- 多个filter执行时，使用的是同一个requst对象和response对象
- filter和请求的资源在同一线程内





# filter匹配方式

可以使用`*`来匹配某个目录下的所有文件，也可以使用例如 `*.html`的方式进行后缀匹配，这里的后缀不仅仅代表文件类型后缀，可以是任意字符串，filter的匹配只关心请求地址，不关心该资源是否实际存在





# ThreadLocal

threadLocal类可以给当前线程关联一个数据

类似于map，threadLocal的key固定是当前线程名，我们可以通过set和get方法设置和获取value，即可以设置一个与当前线程关联的数据

注意一个threadLocal只能设置一个数据，且我们一般把threadLocal设置为static