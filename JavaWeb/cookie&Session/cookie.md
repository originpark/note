# cookie概述

cookie本质是键值对，当用户端第一次请求某个网站时，该网站可以给用户端发送cookie，cookie会保存在用户端的主机中，当用户端第二次及以后再去访问该网站时，浏览器会先查找用户端是都有该网站所对应的cookie（至于浏览器如何做到这一点就不得而知了。。），如果有，则浏览器会自动在请求的请求头中添加一个cookie键值对请求头，服务端接收到cookie请求头数据就能预先得到用户端的一些数据，例如我们访问bilibili时，会自动登录上我们的账号，而不需要每次访问bilibili都要先登录。







# 设置cookie

在servlet中使用response给客户端设置cookie,注意cookie的值不能包含各种符号以及中文

```java
Cookie cookie = new Cookie("key1", "value1");
resp.addCookie(cookie);
```







# 获取cookie

通过request对象获取请求头中的cookie

```java
Cookie[] cookies = req.getCookies();
for (Cookie cookie : cookies) {
    System.out.println(cookie.getName() + " " + cookie.getValue());
}
```





# 修改cookie值



直接new一个与需要修改的cookie同名的cookie即可覆盖原本的cookie

```java
Cookie cookie = new Cookie("key1", "newValue1");
```



通过setValue方法修改值

```java
Cookie[] cookies = req.getCookies();
for (Cookie cookie : cookies) {
    cookie.setValue("newCookie2");
    resp.addCookie(cookie);
}
```





# cookie生命控制

即管理cookie什么时候被删除

通过setMaxAge()方法来控制cookie的删除时间

```java
cookie.setMaxAge(-1);//-1为默认值，表示关闭浏览器时删除该cookie
cookie.setMaxAge(0);//0表示立即删除
cookie.setMaxAge(60 * 60);//整数表示cookie存活的时间，单位是秒
```

注意设置完之后需要调用addcookie方法完成更新



# path

使用setPath方法来实现对cookie的过滤

例如：

```java
cookie.setPath(req.getContextPath() + "/test");
```

此时，只有当请求路径中包含了req.getContextPath() + "/test"的请求才能获取该cookie，当然req.getContextPath() + "/test/abc"等等也能获取该cookie，因为其包含了req.getContextPath() + "/test"，而req.getContextPath()就不能获取该cookie

