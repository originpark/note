servlet类，是java提供的接口，供第三方实现

```java
package javax.servlet;

import java.io.IOException;

public interface Servlet {
    void init(ServletConfig var1) throws ServletException;

    ServletConfig getServletConfig();

    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;

    String getServletInfo();

    void destroy();
}
```



GenericServlet，继承Servlet，也是servlet规范的一员，是一个抽象类，其中的抽象方法是service方法，因为这个方法最常用，且经常需要其实现子类去重写。





# servletConfig

GenericServlet中有一个ServletConfig属性，该对象是是在创建Servlet对象后，由tomcat传给init方法的，GenericServlet中的init方法将传进来的config对象，赋给当前对象的config属性，这样就可以在Servlet的所有方法中使用config对象了

当子类重写init方法时，最好重写init()方法，因为在init(ServletConfig config)方法中会帮我们调用init()方法，而如果重写init(ServletConfig config)方法的话，就需要手动调用super.init(config)以获取config对象

```java
package javax.servlet;

import java.io.IOException;
import java.io.Serializable;
import java.util.Enumeration;

public abstract class GenericServlet implements Servlet, ServletConfig, Serializable {
    private static final long serialVersionUID = 1L;
    private transient ServletConfig config;

    public GenericServlet() {
    }

    public void destroy() {
    }

    public String getInitParameter(String name) {
        return this.getServletConfig().getInitParameter(name);
    }

    public Enumeration<String> getInitParameterNames() {
        return this.getServletConfig().getInitParameterNames();
    }

    public ServletConfig getServletConfig() {
        return this.config;
    }

    public ServletContext getServletContext() {
        return this.getServletConfig().getServletContext();
    }

    public String getServletInfo() {
        return "";
    }

    public void init(ServletConfig config) throws ServletException {
        this.config = config;
        this.init();
    }

    public void init() throws ServletException {
    }

    public void log(String msg) {
        this.getServletContext().log(this.getServletName() + ": " + msg);
    }

    public void log(String message, Throwable t) {
        this.getServletContext().log(this.getServletName() + ": " + message, t);
    }

    public abstract void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;

    public String getServletName() {
        return this.config.getServletName();
    }
}
```



servletConfig对象是javaweb的接口之一，tomcat的org.apache.catalina.core.StandardWrapperFacade类实现了该接口。

ServletConfig对象由tomcat创建，每当创建一个servlet对象时，就会同时创建一个与该servlet对应的servletconfig对象。

servletconfig对象中包含的信息就是web.xml中servlet标签中的内容，例如servlet-name, init-parameter





# servletContext

- 获取servletContext对象，调用ServletConfig的getServletContext方法
- servletContext包含了整个web工程的所有servlet的信息，即一个工程中的所有servlet共享一个servletContext对象