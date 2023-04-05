# Tomcat概述

Tomcat是Web服务器，Web服务器不止Tomcat一种



# 目录介绍

- bin 存放可执行程序
- fonf 存放配置文件
- lib 存放jar包
- logs 存放服务器运行时输出的日志信息
- temp 存放临时数据
- webapps 存放部署的web工程
- work tomcat工作时的目录，用来存放tmocat运行时jsp翻译为Servlet的源码，和Session钝化（序列化）的目录



# hello，tomcat



## 启动tomcat

点击bin目录下的startup.bat以启动tomcat

注意启动tomcat需要配置好JAVA_HOME环境变量

另一种启动Tomcat的方式是在命令行中进入bin目录，然后输入catalina run





## 停止Tomcat

一旦关闭了startup.bat窗口，Tomcat就会关闭

通常做法是点击bin目录下的shutdown.bat来停止Tomcat服务





## Tomcat端口号

Tomcat默认的端口号是8080

这一设置在conf目录下的servlet.xml文件下，可以在这里修改端口号

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```





## 访问tomcat

在浏览器中访问本机的8080端口

http://localhost:8080

http://127.0.0.1:8080

http://10.15.208.53:8080





# 部署Tomcat

在webapps目录下的每一个文件就是一个工程，因此在webapps目录下创建文件夹即为创建工程

当我们使用http://localhost:8080访问tomcat时，实际上进入的是webapps（里面的ROOT项目）目录，想要访问我们的web项目，只需要在该地址基础上添加路径即可，例如，http://localhost:8080/ProjectName/index.html

当地址只写到项目名，例如http://localhost:8080/ProjectName，那么会默认进入该项目的index.html页面



另一种部署Tomcat的方式，在conf\Catalina\localhost目录下创建一个xml文件，例如test.xml

```xml
<Context path="/test" docBase="E:\htmlExercise\exe" />
```

path即为文件名，docBase即该项目所在的路径

这样在访问时，可以输入以下url来访问该项目

http://10.15.208.53:8080/test/index.html





# IDEA中创建Web工程

在idea中配置好Tomcat后，创建web工程（省略步骤）

此时目录结构如下，即比普通java模块多出了一个web文件夹，我们习惯在WEB-INF目录下创建一个lib目录用来存放jar包

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220405170241755.png" alt="image-20220405170241755" style="zoom:50%; margin-left:0;" />



- src 存放我们自己写的java源代码

- web 存放web工程的资源文件，例如html，css，js等文件
- WEB-INF 是一个受服务器保护的目录，浏览器无法直接访问此目录
- web.xml 整个web工程的配置文件，在这里配置很多web工程的组件
- lib 存放第三方jar包



此时启动项目，打开的是index.jsp页面



