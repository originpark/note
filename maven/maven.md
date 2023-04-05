# maven概述

maven帮我们完成项目中的构建和依赖

构建:指的是使用项目中的原材料(java源码, html文件, 配置文件......)来生产产品(一个web项目)的过程

依赖:jar包的下载,jar包之间的依赖关系,jar包之间的冲突





# maven核心程序配置



下载maven核心程序

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220606235108927.png" alt="image-20220606235108927" style="zoom:50%; margin-left:0;" />



在conf目录中的setting.xml中,先配置本地仓库路径,本地仓库就是用来存各种jar包的地方

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220606235151139.png" alt="image-20220606235151139" style="zoom:67%;margin-left:0;" />





Maven 下载 jar 包默认访问境外的中央仓库，而国外网站速度很慢。改成阿里云提供的镜像仓库, 访问国内网站，可以让 Maven 下载 jar 包的时候速度更快

配置方式为找到mirrors标签,删除原有的实例mirror,加入下面的mirror

```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```





如果按照默认配置运行，Java 工程使用的默认 JDK 版本是 1.5，而我们熟悉和常用的是 JDK 1.8 版本

配置方式为,将 profile 标签整个复制到 settings.xml 文件的 profiles 标签内

```xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```





maven由java编写,因此JAVA_HOME环境变量需要配置好,同时需要配置MAVEN_HOME,并在path中添加MAVEN_HOME的bin目录









# maven中的坐标

maven使用三个向量来定位maven仓库中的一个唯一jar包

- groupid 公司或组织
- artifactid 公司或组织的某个项目的某一个模块
- version 项目的版本号







# 命令行创建maven的java工程

首先创建一个用于存放项目的文件夹,然后新建一个项目文件夹

在命令行使用命令生成maven工具

`mvn archetype:generate`

mvn是主命令 archetype:generate是子命令 archetype表示使用的插件 generate表示目标



执行命令后生成的项目 

**groupid**:com.origin.mavenExe 

**artifactid**:maven-java

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220607015950932.png" alt="image-20220607015950932" style="zoom:67%; margin-left:0;" />



src下包含main和test两个目录,main用于放java主程序,test用于放测试程序





将pom.xml中junit的version改为比较适合的4.12

src中main和test文件夹最里面的两个app.java和appTest.java可以删掉





# pom.xml

pom即project object model项目对象模型

```xml
<!--project为根标签,表示对当前工程进行管理-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!--代表pom.xml所采用的标签结构-->
  <modelVersion>4.0.0</modelVersion>

    <!--坐标信息-->
  <groupId>com.origin.mavenExe</groupId>
  <artifactId>maven-java</artifactId>
  <version>1.0-SNAPSHOT</version>
    <!--表示当前工程打包的方式,这里表示生成jar,表示java工程,war表示web工程-->
  <packaging>jar</packaging>

  <name>maven-java</name>
  <url>http://maven.apache.org</url>

    <!--在maven中定义属性值-->
  <properties>
      <!--在构建过程中读取源码时采用的字符集-->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

    <!--配置依赖信息-->
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
        <!--scope用于配置当前依赖的范围-->
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```





# maven构建命令

主程序编译 `mvn compile`

编译后会生成一个target目录, classes中就是编译过后的class文件

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220607131007367.png" alt="image-20220607131007367" style="zoom:67%;margin-left:0;" />







测试程序编译 `mvn test-compile`

编译后target目录中会生成一个test-classes目录

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220607131347981.png" alt="image-20220607131347981" style="zoom:67%; margin-left:0;" />





清理target `mvn clean` 

清除target目录





执行测试程序 `maven-test`

编译并执行所有的测试程序





打包 `mvn package`

项目打包成jar包保存在target目录下



安装 `mvn-install`

安装的效果是将本地构建过程中生成的 jar 包存入 Maven 本地仓库





# 创建maven版的web工程

命令行执行命令

```log
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4
```

在与maven-java项目同级目录下创建一个maven-web项目

maven-web\src\main下有一个webapp目录，这个目录就是idea项目中的web目录，我们可以创建一个与之同级的java目录用来存放java源码



在java目录下创建包编写一个servlet，在WEB-INF目录下给该servlet配置地址

将servlet-api.jar的依赖信息加入pom.xml以导入servlet的依赖



执行mvn compile

执行mvn package后，target目录下就会有一个war包和一个解压后的项目文件夹

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220607190229825.png" alt="image-20220607190229825" style="zoom:50%;margin-left:0;" />

可以将该文件夹部署到tmocat的webapps下，然后启动tomcat访问





# 让web工程依赖java工程

Java工程压缩为jar包，放在web工程的WEB-INF目录下的lib目录中

即将java工程的三个坐标写入web工程的pom.xml的dependencies中,然后编译打包,就能将java工程的jar包加入到WEB-INF下的lib中





# 查看依赖

`mvn dependency:list` 以列表形式查看依赖

`mvn dependency:tree` 以树的形式查看依赖





# 依赖范围

控制依赖范围的标签位于pom.xml/dependencies/dependency/scope

标签可选值有compile test provided



compile 和 test 对比

|         | main目录（空间） | test目录（空间） | 开发过程（时间） | 部署到服务器（时间） |
| ------- | ---------------- | ---------------- | ---------------- | -------------------- |
| compile | 有效             | 有效             | 有效             | 有效                 |
| test    | 无效             | 有效             | 有效             | 无效                 |



compile 和 provided 对比

|          | main目录（空间） | test目录（空间） | 开发过程（时间） | 部署到服务器（时间） |
| -------- | ---------------- | ---------------- | ---------------- | -------------------- |
| compile  | 有效             | 有效             | 有效             | 有效                 |
| provided | 有效             | 有效             | 有效             | 无效                 |





# 依赖的传递

依赖传递就是指当我们引入一个依赖a时,如果a依赖b,那么这个b就会自动帮我们导入



在 A 依赖 B，B 依赖 C 的前提下，C 是否能够传递到 A，取决于 B 依赖 C 时使用的依赖范围。

- B 依赖 C 时使用 compile 范围：可以传递
- B 依赖 C 时使用 test 或 provided 范围：不能传递，所以需要这样的 jar 包时，就必须在需要的地方明确配置依赖才可以。





# 依赖的排除

a依赖b和c,b和c同时依赖不同版本的d,此时对于a来说,只需要传递b或c所依赖的一个d即可,因此就存在排除某个依赖的操作

具体做法是,如果想排除b依赖的d,就在a对b的依赖配置中添加排除标签,如果想排除c依赖的d,就在a对c的依赖配置中添加排除标签

```xml
<dependency>
    <groupId>com.origin.mavenExe</groupId>
    <artifactId>maven-java</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--使用exclusions标签配置依赖的排除-->
    <exclusions>
        <!--每个exclusion标签就是一个具体的依赖排除,里面写要排除依赖的groupId和artifactId,不需要写版本号-->
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```





# 继承

maven的继承就是项目的继承,例如一个大的项目中有很多个子项目,很多子项目可能都需要用到某个共同的依赖,此时就可以在父项目的pom.xml中定义好这个依赖的版本,所有的子项目都使用父项目规定的版本



pom.xml中packing标签值为pom的工程是用来管理其他工程的工程,就是一个父工程

其他工程内部是不能创建新工程的,但是打包方式为pom的工程内部可以创建子工程,且创建完之后,父工程的pom内会自动加入models标签

```xml
<modules>  
    <module>subProject01</module>
    <module>subProject02</module>
</modules>
```

同时子工程的pom中也会加入parent标签

```xml
<parent>
    <groupId>com.origin.mavenExe</groupId>
    <artifactId>SuperProject</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>
```





子项目的pom中,如果groupId和version与父项目相同,则可以省略





在父工程中使用dependencyManagement标签配置依赖的统一管理

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.0.0.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```





在子工程中使用父工程配置好的依赖时可以省略版本号,省略版本号意味着由父工程来决定该依赖的版本



可以在父工程的properties标签中自定一个版本标签,然后再需要使用其值的地方使用${}表达式进行引用







# 使用IDEA创建maven工程

