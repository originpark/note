# jsp的九大内置对象

- `request` 请求对象
- `response` 相应对象
- `pageContext` jsp的上下文对象
- `session` 会话对象
- `application` ServletContext对象
- `config` ServletConfig对象
- `out` jsp输出流对象
- `page` 指向当前jsp对象
- `exception` 异常对象



# jsp四个域对象

- `pageContext`  PageContextImpl类
- `requst`   HttpServletRequest类
- `session` HttpSession类
-  `application`  ServletContext类



域对象就是用以存取数据的对象，这四个域对象功能一样，都是用来像map一样存取key-value，不同的是，他们存取数据的范围不同

pageContext 当前页面范围内

request 一次请求内

session 一次会话范围内

application 整个web工程内



这四个域在使用时，如果key相同，则会优先得到范围下的域的数据







# EL表达式概述

EL全称是 Expression Language，即表达式语言

EL主要用于代替jsp中的表达式脚本，在jsp页面中进行数据输出





表达式脚本和EL表达式输出比较
```jsp
<body>
    <%request.setAttribute("key", 100);%>
    // 使用表达式脚本输出key
    <%=request.getAttribute("key")%>
    // 使用EL表达式输出request中的key
    ${key}
</body>
```



可以看出EL的写法更简洁。同时EL会将null处理为空串，而表达式脚本则会直接显示null

EL表达式主要输出的是域对象中的数据，在jsp中，即pageContext，request，session，application，如果同时在四个域中保存了相同key的属性，那么则会按照范围从小到大输出。




# 输出对象

创建一个user对象，将其存为pageContext的属性，使用EL通过域对象pageContext的key输出user对象的name属性
```jsp
<body>
<%
    User user = new User("origin", 19);
    pageContext.setAttribute("key", user);
%>
${key.name}
</body>
```



注意，EL并不是直接访问key的name属性，而是通过user类提供的getName方法来得到name属性，因此如果在User类中没有提供getName方法，那么就无法访问该属性



# EL运算符

常见的算数运算符，逻辑运算符都能用



**empty运算符**
判断一个数据是否为空，如果为空，则输出true，否则输出false

这里判断是否为空不仅限于null，还包括空字符串，或者数组，集合的长度为0时



**[]**
用以标识其内的内容为属性

```jsp
<body>
<%
   Map map = new HashMap();
   map.put("a.a.a", "value");
   request.setAttribute("map", map);
%>
// 这里的a.a.a由于有'.'，所以EL会解析错误，因此用['']来标  // 识a.a.a是一个属性
${map['a.a.a']}
</body>
```













# EL的11个隐含对象

- `pageContext`
- `pageScope`
- `requestScope`
- `sessionScope`
- `applicationScope`
- `param`
- `paramValues`
- `header`
- `headerValues`
- `cookie`
- `initParam`







## 域对象

pageScope，requestScope，seccionScope，applicationScope

他们四个分别可以获取pageContext，request，session，application的属性

```jsp
<body>
<%
    pageContext.setAttribute("key1", "pageContext1");
    request.setAttribute("key1", "request1");
%>
// 没有指定是哪个域对象，那么默认从范围最小的开始寻找，即先找pageContext有没有key1
${key1}
// 通过requestScope指定了要找request域的key1
${requestScope.key1}
</body>
```





## pageContext

pageContext对象用于获取jsp的九大内置对象





## param

用于获取请求参数，但是只能获取一个参数，例如如果有多个key为name的属性，那么param只能获取其中的第一个name



## paramValues

 用于获取请求参数，可以获取多个同key的属性，通过`paramValues.key[index]`的方式获取



## header

用于获取请求头，例如`header.['user-agent']`就是获取请求头中的浏览器信息，注意这里的key要由于会有特殊字符，所以最好用['']包起来





## headerValues

用于获取多个同key的请求头，与param和paramValues的关系类似



## initParam

获取web.xml中配置的context-param参数

```xml
<context-param>
    <param-name>key</param-name>
    <param-value>value</param-value>
</context-param>
```

