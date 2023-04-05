# listener概述

listener即为监听器，同servlet，Filter一样为javaEE的三大组件之一，是一个接口

listener的作用是监听某种事务的变化，然后通过回调函数，做一些相应的处理，反馈给客户端







# ServletContextListener



```java
public interface ServletContextListener extends EventListener {
    // 在servletContext对象创建之后立即调用，做初始化
    void contextInitialized(ServletContextEvent var1);

    // 在servletContext对象销毁之后立即调用，
    void contextDestroyed(ServletContextEvent var1);
}
```



**使用ServletContextListener**



先创建一个类实现ServletContextListener，并实现该方法

```java
public class myServletContextListenerImpl implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("Listener is comming");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("listener is gone");
    }
}

```





在web.xml中配置listener的类名

```xml
<listener>
    <listener-class>
        com.origin.ListenerTest.myServletContextListenerImpl
    </listener-class>
</listener>
```

