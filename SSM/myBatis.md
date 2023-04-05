>@author origin
>
>@QQ 1223003136
>
>:v::eyes::v:





# mybatis概述

mybatis是封装了jdbc的java持久层框架







# hello,mybatis



## 创建maven工程

首先创建maven工程,打包方式为jar,引入相关依赖

```xml
<dependencies>
    <!-- Mybatis核心 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.10</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.11</version>
    </dependency>
</dependencies>
```



## 创建核心配置文件

在resource目录下创建核心配置文件mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--environment标签用于配置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```



## 创建mapper接口

mapper接口就相当于我们从前创建的Dao层,只不过在mapper接口中,只需要写接口,不需要写实现类了,每张表对应一个mapper接口

编写UserMapper接口,声明一个添加user的方法

```java
public interface UserMapper {
    int addUser();
}
```



## 创建映射文件

在resource目录下创建映射xml文件,每个mapper接口对应一个映射文件,映射文件名和mapper接口保持一致

根标签mapper的namespace属性用于匹配一个mapper接口,其内可以写增删改查四个标签,标签的id匹配mapper中的某个方法,在增删改查标签中写sql,如果是查询操作,则需要写resultType标签用于sql语句返回值的类型

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.origin.mapper.UserMapper">
   <insert id="addUser">
      INSERT INTO t_user VALUES(null, 'origin', '122300', 20, '男')
   </insert>
    <select id="queryUser" resultType="com.origin.domain.User">
      SELECT * FROM t_user WHERE `username` = 'gene'
   </select>
</mapper>
```



创建完映射文件之后需要在核心配置文件中引入映射文件

```xml
<mappers>
    <mapper resource="mappers/UserMapper.xml"/>
</mappers>
```



## 执行sql

在测试方法中执行sql

```java
public void test01() throws IOException {
    //读取核心配置文件
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    //获取SqlSessionFactoryBuilder对象
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    //获取SqlSessionFactory对象
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
    //获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //获取mapper接口的实现类对象实例
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    //测试功能
    int ret = userMapper.addUser();
    //默认开启事务,因此要手动提交事务
    sqlSession.commit();
    System.out.println("受影响的行数: " + ret);
}
```



sqlSession对象默认是手动提交事务,如果想要自动提交事务,需要在openSession方法中将autoCommit设置为true,具体做法直接在参数中填入一个true即可

```java
SqlSession sqlSession = sqlSessionFactory.openSession(true);
```





# 核心配置文件



## 基本结构

核心配置文件的基本结构如下

```xml
<configuration>
	<environments default="development01">
        <environment id="development01">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
        <environment id="development02">
        </environment>
    </environments>
    <mappers>
        <mapper></mapper>
    </mappers>
</configuration>
```



其中,environments标签用于配置链接数据库的环境,可以有多个environment标签,即可以有多个数据库链接环境,但是实际只会使用一个,因此在environmnets中设置default标签来标注默认使用的环境,与environment标签的id属性相匹配

transactionManager标签用于设置事务管理方式

DataSource标签中的type属性,POOLED表示使用连接池,UNPOOLED表示不使用连接池

mapper标签用于引入映射文件



## properties读取datasource

datasource中的连接信息可以通过读取properties文件的方式获取

在resources目录下创建properties文件

```properties
datasource.driver=com.mysql.cj.jdbc.Driver
datasource.url=jdbc:mysql://localhost:13306/MyBatis?rewriteBatchedStatements=true&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
datasource.username=root
datasource.password=origin
```



在核心配置文件中引入配置文件

```xml
<properties resource="datasource.properties"></properties>
```



使用表达式来代替datasource标签中的各配置信息

```xml
<dataSource type="POOLED">
    <property name="driver"   value="${datasource.driver}"/>
    <property name="url"      value="${datasource.url}"/>
    <property name="username" value="${datasource.username}"/>
    <property name="password" value="${datasource.password}"/>
</dataSource>
```



## typeAlias&package

typeAlias标签可以给类设置别名

在核心配置文件中使用typeAliases标签,内部设置多个typeAlias标签,每个typeAlias标签给一个类设置一个别名,type为该类的全类名,alias为我们给该类起的别名

```xml
<typeAliases>
    <typeAlias type="com.origin.domain.User" alias="user"></typeAlias>
</typeAliases>
```

别名可以使用在映射文件中的typename标签中,即将全类名换成别名



核心配置文件中的标签书写是有顺序的,typeAlias标签必须写在properties标签之后(如果有properties的话)

alias属性不区分大小写,如果不设置alias则默认以类名作为别名



在typeAliases标签中还有一个package标签,该标签用于标注一个包,作用是一次性将该包下的所有类都设置一个默认别名(类名)

```xml
<package name="com.origin.domain"/>
```



package标签还可以写在mappers标签中,用于引入一个文件夹下的所有映射文件

```xml
<mappers>\
	<package name="com.origin.mapper"/>
</mappers>
```

要求映射文件在resource目录下有和mapper接口相同的目录结构

<img src="..\..\images\image-20220626103750740.png" alt="image-20220626103750740" style="zoom:50%; margin-left: 0;" />





# 获取参数

即如何在映射文件中获取上层传递过来的参数并拼接到sql语句中

通过在sql语句中使用表达式来传入参数,表达式有两个, `#{}`和`${}`

`#{}`表示占位符,`${}`表示字符串拼接





**单个参数**

例如mapper接口中有以下方法

```java
User queryUserByUsername(String username);
```

在sql中使用占位符表达式来获取参数

```sql
SELECT * FROM t_user WHERE `username` = #{username}
```



**多个参数**

如果接口方法中有多个参数,则这些参数会被封装到map集合中,并以`arg0`,`arg1`或者`param1`, `param2`等等为key,

因此此时在获取参数时,表达式内的名称就要填入这些key

```sql
SELECT * FROM t_user WHERE `username` = #{arg0} AND `password` = #{arg1}
```



**参数含有map集合**

当参数中含有map集合时,就可以通过map集合的key来访问map中的数据

例如有以下方法

```java
User checkLogin(Map<String, Object> map, Integer n);
```



则在映射文件中sql如下,这里的param1就是map

```sql
SELECT * FROM t_user WHERE `username` = #{param1.username} AND `password` = #{param1.password}
```



**参数为对象**

例如当参数为一个User对象时,可以通过属性名来访问该对象的属性



**@param注解**

在mapper接口方法中给参数标注param注解,即可在sql中用该注解值访问对应参数

```java
User query(@Param("username") String username, @Param("password") String password);
```



# 查询

查询使用select标签,且一定要有resultType属性,查询返回的是什么,resultType就写什么,并且mabatis为一些常用类预先做了一些类型别名

例如

```xml
<select id="queryCount" resultType="java.lang.Integer">
    SELECT COUNT(*) FROM t_user
</select>
```

这里的resultType可以用Integer的别名代替

```xml
<select id="queryCount" resultType="Integer">
    SELECT COUNT(*) FROM t_user
</select>
```





如果查询的结果是一个对象,那么resultType中可以填对象,也可以填map

如果resultType中填入对象,则mapper方法返回值是一个对象

如果resultType中填入map,则mapper方法返回值是以对象的属性名为key,属性值为value的map

如果查询的结果是多个对象,resultType仍然填入对象或map,mapper方法的返回值则需要填入对象list或者map的list



**@MapKey注解**

MapKey注解标志于mapper方法上,例如

```xml
<select id="queryAllUser" resultType="map">
    SELECT * FROM t_user
</select>
```

```xml
@MapKey("id")
Map<String, Object> queryAllUser();
```

表示返回的是一个map,该map以每个对象对应的map的id属性为key,以每个map为value





# 特殊sql



## 模糊查询

即like运算符

此时使用参数不可用`#{}`,有以下两种方式

```sql
SELECT * FROM t_user WHERE `username` LIKE '%${username}%'
SELECT * FROM t_user WHERE `username` LIKE "%"#{username}"%"
```





## 获取自增主键

在映射文件的sql标签中,useGeneratedKeys属性设置为true表示我们需要获取该sql语句中自增的主键的值

由于增删改的返回值只能是影响的行数,因此需要使用keyProperty来设置我们要将获取到的主键值放在哪个属性中



例如此处,创建一个user对象,id为主键,为null

```java
User user = new User(null, "alice", "122300", 20, "女");
userMapper.addUser(user);
```



调用addUser方法,useGeneratedKeys="true"表示获取该sql中的主键值,keyProperty="id"表示将获取到的主键值保存在传进来的user对象的id属性中,因此当执行addUser方法过后,该user对象的id属性不再为null,而是我们获取到的主键值

```xml
<insert id="addUser" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO t_user
    VALUES (null, #{username}, #{password}, #{age}, #{gender})
</insert>
```





# 自定义映射

当在数据库中定义的字段名和其对应domian中的属性名不一致时,在执行查询操作返回一个domian对象时,查到的字段值就不能正确找到对应的属性值并赋值,这时候该属性值就会为null



## mybatis全局设置

常见的例如在数据库中,字段名我们使用下划线:emp_name

而在java中,对应的属性名我们使用驼峰:empName

对于这种情况,可以在mybatis-config.xml中添加如下标签

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

setting标签表示mybatis的全局配置,写在properties之后,typeAlias之前

mapUnderscoreToCamelCase属性为true时表示在映射时自动将下划线转为驼峰



## resultMap代替resultType

除了上面的方式,更合适的是使用resultMap来代替resutType

先在映射文件中配置好某个domian类的resultMap标签

```xml
<resultMap id="EmpResultMap" type="employee">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="salary" column="salary"></result>
    <result property="did" column="did"></result>
</resultMap>
```

type为该resultMap要映射的domain类,id标签用于设置主键的映射关系,result标签用于设置普通字段/属性的映射关系,property中填写属性,column中填写字段

使用时,将resultType标签替换为resultMap标签即可,其值为某个resultMap的id

```xml
 <select id="queryEmpByEmpName" resultMap="EmpResultMap">
     SELECT * FROM t_emp WHERE `emp_name` = #{empName}
</select>
```





## 多对一映射

即当domain对象中还包含有其他的对象属性,我们需要给该对象属性的属性也添加映射关系

<img src="../../images/Snipaste_2022-07-07_20-26-46.png" style="zoom: 50%; margin-left: 0">





property标签可以进行级联赋值,即如果domain对象中含有其他对象属性的话,可以使用级联赋值的方式给该对象属性的属性赋值

```xml
<result property="dept.did" column="did"></result>
```



除了级联赋值,在resultMap标签中有一个association标签是专门用来处理这种情况的

```xml
<resultMap id="EmpResultMap" type="employee">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="salary" column="salary"></result>
    <result property="did" column="did"></result>
    <association property="dept" javaType="Department">
        <id property="did" column="did"></id>
        <result property="deptName" column="dept_name"></result>
    </association>
</resultMap>
```

其中,association的prperty属性表示要处理的属性名,javaType属性表示该属性的类型

这种情况要求我们的sql查询必须要同时查询出员工信息和其部门信息

使用分步查询可以简化sql



**分步查询**

在employee.xml中,resultMap中同样设置一个association标签,property属性表示要映射的属性,select中填写一个sql语句的唯一标识,即类名.方法名,,column中填入分步查询的条件

```xml
<!--employeeMapper.xml-->
<resultMap id="step1" type="employee">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="salary" column="salary"></result>
    <result property="did" column="did"></result>
    <association property="dept" select="com.origin.mapper.DeptMapper.queryEmpByStep2" column="did">
    </association>
</resultMap>
<select id="queryEmpByStep1" resultMap="step1">
    SELECT * FROM t_emp WHERE eid = #{eid}
</select>
```



```xml
<!--depeMapper.xml-->
<resultMap id="step2" type="Department">
    <id property="did" column="did"></id>
    <result property="deptName" column="dept_name"></result>
</resultMap>

<select id="queryEmpByStep2" resultMap="step2">
    SELECT * FROM t_dept WHERE did = #{did}
</select>
```



当我们调用queryEmpByStep1时,先执行其sql查询到员工信息,并给相应属性赋值

对于dept属性,再以column值为参数, 执行select设置的sql语句,用该sql查询到的结果给dept属性中的属性赋值



**延迟加载**

分步查询的好处是可以实现延迟加载,即对于分步查询不是每次都把所有sql都执行,假如我们不获取dept,就不会执行第二步的sql

通过在mybatis-config.xml中设置全局setting以开启

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```



也可以在association标签中设置fetchType属性来控制延迟加载,当开启了全局的延迟加载之后，可以通过该属性手动控制延迟加载的效果，fetchType="lazy(延迟加载)|eager(立即加载)"





## 一对多映射

即对象中有一个集合,我们需要给该集合中的每个对象也设置映射

<img src="../../images/Snipaste_2022-07-11_16-46-09.png" style="zoom: 50%; margin-left: 0;">





**collection**

此时设置resultMap,使用collection标签来给empList设置映射,ofType表示list中的元素的类型

```xml
<resultMap id="DeptAndEmpResultMap" type="department">
    <id property="did" column="did"></id>
    <result property="deptName" column="dept_name"></result>
    <collection property="empList" ofType="Employee">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="salary" column="salary"></result>
        <result property="did" column="did"></result>
    </collection>
</resultMap>
```



**分步查询**

分步查询同多对一的分步查询相同,在collection标签中添加select属性以加入第二步执行的sql





# 动态SQL

即在sql语句中添加一些标签使能够动态地生成sql语句



## if

if标签内的语句只有当test返回true时才会拼接

```xml
<select id="queryEmpByDynamicSql" resultType="employee">
    select * from t_emp where 1=1
    <if test="eid != null and eid != ''">and eid = #{eid}</if>
    <if test="empName != null and empName != ''">and emp_name = #{empName}</if>
    <if test="salary != null and salary != ''">and salary = #{salary}</if>
</select>
```

where后面加个1=1是为了防止sql语法错误,因为可能会出现第一个if不满足条件从而sql变成了`where and`





## where

当where标签中有语句时,将自动在sql中添加where关键字,并且会自动除去where后面紧跟的`and`或者`or`

当where标签中没有语句时,将不会添加where关键字到sql中

```xml
<select id="queryEmpByDynamicSql" resultType="employee">
    select * from t_emp
    <where>
        <if test="eid != null and eid != ''">and eid = #{eid}</if>
        <if test="empName != null and empName != ''">and emp_name = #{empName}</if>
        <if test="salary != null and salary != ''">and salary = #{salary}</if>
    </where>
</select>
```

使用where标签就可以避免使用1=1这样的不优雅的语句





## trim

prefix：在trim标签中的内容的前面添加某些内容  

suffix：在trim标签中的内容的后面添加某些内容 

prefixOverrides：在trim标签中的内容的前面去掉某些内容  

suffixOverrides：在trim标签中的内容的后面去掉某些内容

若trim中的标签都不满足条件，则trim标签没有任何效果

```xml
<select id="queryEmpByDynamicSql" resultType="employee">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="eid != null and eid != ''">eid = #{eid} and</if>
        <if test="empName != null and empName != ''">emp_name = #{empName} and</if>
        <if test="salary != null and salary != ''">salary = #{salary} and</if>
    </trim>
</select>
```





## choose&when&otherwise

类似于switch case,在choose中,从前往后依次判断,只要找到满足条件的,后面的就都不执行了,otherwise就相当于default

```xml
<select id="queryEmpByDynamicSql" resultType="employee">
    select * from t_emp
    <where>
        <choose>
            <when test="empName != null...">emp_name = #{empName}</when>
            <when test="salary != null and salary != ''">salary = #{salary}</when>
            <when test="did != null and did != ''">did = #{did}</when>
            <otherwise>
                eid = 1
            </otherwise> 
        </choose>
    </where>
</select>
```



## foreach

collection：设置要循环的数组或集合  

item：表示集合或数组中的每一个数据  

separator：设置循环体之间的分隔符，分隔符前后默认有一个空格，如` , `

open：设置foreach标签中的内容的开始符  

close：设置foreach标签中的内容的结束符



批量删除

```xml
<!--Integer deleteEmpByDynamicSql(@Param("eIDs")Integer[] eIds);-->
<select id="deleteEmpByDynamicSql" resultType="int">
    delete from t_emp where eid in
    <foreach collection="eIDs" item="eid" open="(" close=")" separator=",">
        #{eid}
    </foreach>
</select>
```



批量添加

```xml
<!--    Integer addEmpByDynamicSql(@Param("empList")List<Employee> employees);-->
<select id="addEmpByDynamicSql" resultType="int">
    insert into t_emp values
    <foreach collection="empList" item="emp" separator="," >
        (null, #{emp.empName}, #{emp.salary}, #{emp.did})
    </foreach>
</select>
```





## sql

sql标签用于define一些常用的sql

```xml
<sql id="empColumns">eid, emp_name, salary, did</sql>
```



使用时用include标签

```sql
select <include refid="empColumns"></include> from t_emp
```



# mybatis缓存



## 一级缓存

一级缓存是默认开启的,作用范围为同一个sqlSeesion

即用同一个sqlSession查询到的数据是会被缓存的,二次查询会直接使用缓存中的数据而不会在去数据库中查询

同一个SqlSession两次查询期间执行了任何一次增删改操作,就会自动清除一级缓存

手动清空缓存:`sqlSession.clearCache();`



## 二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存

二级缓存开启的条件

1. 在核心配置文件中，设置全局配置属性`cacheEnabled="true"`，默认为true，不需要设置
2. 在映射文件中设置标签`<cache />`
3. 二级缓存必须在SqlSession关闭`sqlSession.close()`或提交`sqlSession.commit()`之后有效
4. 查询的数据所转换的实体类类型必须实现序列化的接口





# 逆向

在pom.xml中添加

```xml
<build>
    <!-- 构建过程中用到的插件 -->
    <plugins>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.0</version>
            <!-- 插件的依赖 -->
            <dependencies>
                <!-- 逆向工程的核心依赖 -->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <!-- 数据库连接池 -->
                <dependency>
                    <groupId>com.mchange</groupId>
                    <artifactId>c3p0</artifactId>
                    <version>0.9.2</version>
                </dependency>
                <!-- MySQL驱动 -->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.27</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```



在resources目录下新建generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
    targetRuntime: 执行生成的逆向工程的版本
    MyBatis3Simple: 生成基本的CRUD（清新简洁版）
    MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3Simple">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:13306/MyBatis?rewriteBatchedStatements=true&amp;useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true"
                        userId="root"
                        password="origin">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.origin.mybatis.pojo" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.origin.mybatis.mapper"
                         targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.origin.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Employee"/>
        <table tableName="t_dept" domainObjectName="Department"/>
    </context>
</generatorConfiguration>
```



运行maven插件,mabatis-generator,神奇的事情发生了,mybatis从此白学



逆向的奢华尊享版生提供了各种不同的crud

```java
//条件查询,传入null代表条件为null,即无条件查询
//List<Employee> employees = employeeMapper.selectByExample(null);
EmployeeExample employeeExample = new EmployeeExample();
EmployeeExample.Criteria criteria = employeeExample.createCriteria();
criteria.andEmpNameEqualTo("origin").andSalaryGreaterThan(10000);
List<Employee> employees = employeeMapper.selectByExample(employeeExample);
```

