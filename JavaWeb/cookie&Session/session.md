# Session概述

浏览器第一次访问某个服务器时，服务器可以设置一个session，该session与该用户端相对应，用户的信息可以保存在该session中，同时服务端回传给浏览器一个sessionID，该seesionID可以是一个cookie，用以唯一标识该用户端，下次当用户端访问该浏览器时，将sessionID一并发送给服务器，服务器通过该seesionID在服务器内部寻找对应的存有该客户端信息的session

注意区分cookie和seesion，cookie是保存在用户端的，session是保存在服务端的





# 常用操作

获取session，如果是第一次访问服务器，则是创建session

```java
HttpSession session = req.getSession();
```



判断session是否是新创建的

```java
boolean isNew = session.isNew();
```



获取sessionID

```java
String id = session.getId();
```



设置attribute

```java
session.setAttribute("key1", "value1");
```



获取attribute

```java
Object key1 = session.getAttribute("key1");
```



# session超时控制

session超时表示两次请求间的时间间隔到达某一值时超时，单位是秒

这里的超时指的是该session在服务器中保存的时长

```java
session.setMaxInactiveInterval(10);//当两次请求间时间间隔大于10秒后超时
```

这里的参数，正数表示设置session的超时时长，负数表示session永不超时，如果想让session立即超时，可以调用invalidate方法



注意，由于服务端是通过请求中发过来的sessionID来匹配对应的session的，而该sessionID实质上是cookie，因此当我们关闭浏览器后重新访问服务器时，虽然session未超时，但是表示sessionID的cookie已经不存在了，所以服务端就无法获取sessionID，从而无法匹配session，此时会重新创建一个session对象