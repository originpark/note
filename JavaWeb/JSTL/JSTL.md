# JSTL概述

JSTL全称是jsp standard tag library，即jsp标准标签库

EL替代的是jsp的表达式脚本，而JSTL替代的是jsp的代码脚本



# JSTL使用环境配置

首先引入两个jar包

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220422210413186.png" alt="image-20220422210413186" style="zoom:80%; margin-left: 0;" />

并将这两个jar包复制到tomcat的bin目录中

将jar包所在文件夹中的tld中的c.tld复制到WEB-INF目录下

在web.xml中配置以下内容

```xml
<jsp-config>
    <!-- 配置c描述文件-对应c标签，这里的taglib-uri对应jsp中引入的uri -->
    <taglib>
        <taglib-uri>http://java.sun.com/jsp/jstl/core</taglib-uri>
        <taglib-location>/WEB-INF/c.tld</taglib-location>
    </taglib>
</jsp-config>
```



最后，在需要使用JSTL的jsp页面最开头导入taglib指令即可

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```





# set

使用set来设置attribute

```jsp
<body>
// scope="page"表示pageScope，var表示key value即value
<c:set scope="page" var="name" value="origin"></c:set>
${pageScope.name}
</body>
```





# if

使用if来实现if判断

```jsp
<c:if test="1 == 1">
    <h1>1==1</h1>
</c:if>
```





# choose&when&otherwise

使用choose和when实现switch case 效果，并且无需break

```jsp
<body>
<%
    request.setAttribute("key", 123);
%>
<c:choose >
    <c:when test="${requestScope.key > 100}">
        key > 100
    </c:when>
    <c:when test="${requestScope.key > 200}">
        key > 200
    </c:when>
    <c:otherwise>
        key < 100
    </c:otherwise>
</c:choose>
</body>
```





# forcach

使用forcach实现遍历1到10，var为遍历的每一个元素的别名，可以任意取，step为遍历步长

```jsp
<c:forEach begin="1" end="10" var="i" step="1">
    ${i}
</c:forEach>
```



使用foreach实现遍历某个域对象的数据

```jsp
<c:forEach items="${Scope.key}" var="item">
    
 </c:forEach>  
```



