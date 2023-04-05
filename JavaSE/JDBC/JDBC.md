# JDBC概述

JDBC是java的一个接口，不同的数据库管理系统可以实现该接口，从而可以使用java程序操作数据库

数据库管理系统将实现了JDBC接口的类打包成jar文件，称为驱动

引入驱动文件，即可使用java操作相应的数据库

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220411201401421.png" alt="image-20220411201401421" style="margin-left: 0;"  />







# 连接数据库



**方式一**

```java
// 注册驱动，创建Driver对象
Driver driver = new Driver();
// 这里的url为要连接到的数据库的地址
// jdbc:mysql://是固定协议
// localhost:13306/atguigudb 表示主机IP，端口号，数据库名
// mysql5版本只要写jdbc:mysql://localhost:13306/atguigudb即可，而mysql8以上的版本后面要加
// 一大堆参数。。我能找到正确写法也是不容易了。。
String url = "jdbc:mysql://localhost:13306/atguigudb?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC";
// 将user和password信息放入properties对象中
// 这里的user和password是固定的key，不可更改
Properties properties = new Properties();
properties.setProperty("user", "root");
properties.setProperty("password", "origin");
// 得到连接
Connection connect = driver.connect(url, properties);
```

注意在jdbc5中，Driver对象全类名为com.mysql.jdbc.Driver，而在jdbc8中，Driver对象全类名为com.mysql.cj.jdbc.Driver，同时jdbc8在url中需要加上右面一长串内容



**方式二**

与方式一的区别在于通过反射来加载Driver对象

```java
Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
Driver driver = (Driver) aClass.newInstance();
```





**方式三**

使用`DriverManager`代替`Driver`来获取连接，具有更号的扩展性

```java
Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
Driver driver =(Driver) aClass.newInstance();
String url = "jdbc:mysql://localhost:13306/atguigudb?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC";
String user = "root";
String password = "origin";
// 注册driver驱动
DriverManager.registerDriver(driver);
// 使用DriverManager获取连接，无需传入properties对象
Connection connection = DriverManager.getConnection(url, user, password);
```



**方式四**

使用Class.forName会自动帮我们完成注册驱动，因此注册驱动这一步可以省略

```java
Class<?> aClass = Class.forName("com.mysql.cj.jdbc.Driver");
Driver driver =(Driver) aClass.newInstance();
String url = "jdbc:mysql://localhost:13306/atguigudb?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC";
String user = "root";
String password = "origin";
// 使用DriverManager获取连接，无需传入properties对象
Connection connection = DriverManager.getConnection(url, user, password);
```





**方式5**

通过配置文件来获取各个数据

```java
Properties properties = new Properties();
properties.load(new FileInputStream("JDBC\\mysql.properties"));
String driver = properties.getProperty("driver");
String user = properties.getProperty("user");
String password = properties.getProperty("password");
String url = properties.getProperty("url");
Class<?> driver1 = Class.forName(driver);
Connection connection = DriverManager.getConnection(url, user, password);
```



# 查询

使用statement对象进行selelct查询

```java
// 连接数据库
Properties properties = new Properties();
properties.load(new FileInputStream("JDBC\\mysql.properties"));
String driver = properties.getProperty("driver");
String user = properties.getProperty("user");
String password = properties.getProperty("password");
String url = properties.getProperty("url");
Class<?> driver1 = Class.forName(driver);
Connection connection = DriverManager.getConnection(url, user, password);
// statement对象用于执行sql语句并返回结果对象
Statement statement = connection.createStatement();
String sql = "select * from employees";
// 执行查询语句时，使用excuteQuery，返回结果集resultSet
ResultSet resultSet = statement.executeQuery(sql);
// 将resultSet看作查询到的表，类似于迭代器，有一个指针一开始指向表的第一行数据的前一行，next方法用于
// 将指针指向下一行，如果没有下一行，则返回false
while (resultSet.next()) {
    // getInt，getString，getDate分别得到本行中相应类型的数据，传入的参数代表列
    int id = resultSet.getInt(1);
    String name = resultSet.getString(2);
    Date date = resultSet.getDate(6);
    System.out.println(id + "  " + name + "  " + date);
}
resultSet.close();
statement.close();
connection.close();
```



statement会有sql注入风险，因此基本上不会使用statement，而是使用PrepareStatement

```java
//省略了连接mysql的步骤
// sql中的?表示占位符，我们可以设置占位符中的内容为某个变量
String sql = "SELECT * FROM employees WHERE employee_id = ?";
//在生成prepareStatement对象时传入sql语句完成预编译
PreparedStatement preparedStatement = connection.prepareStatement(sql);
//setInt表示要设置的占位符内容为int，1代表第一个占位符,id为占位符的内容
preparedStatement.setInt(1, id);
//注意这里的excuteQuery不要再传入sql了
ResultSet resultSet = preparedStatement.executeQuery();
if (resultSet.next()) {
    System.out.println("query success");
} else {
    System.out.println("query failed");
}
resultSet.close();
preparedStatement.close();
connection.close();
```





# DML

使用prepareStatement的excuteUpdate方法执行dml（增删改）语句，返回所影响的行数

```java
int rows = preparedStatement.executeUpdate();
if (rows > 0) System.out.println("success");
```







# jdbc工具类

将连接数据库和关闭资源的工作封装到jdbc工具类中

```java
public class JDBCUtils {
    private static String user;
    private static String password;
    private static String url;
    private static String driver;
    
	// 在静态代码块中读取配置文件
    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileInputStream("JDBC\\mysql.properties"));
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            url = properties.getProperty("url");
            driver = properties.getProperty("driver");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    // 连接数据库
    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    // 关闭资源
    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        try {
            if (resultSet != null) resultSet.close();
            if (statement != null) statement.close();
            if (connection != null) connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```





# 事务

在jdbc中，默认每次执行sql后默认自动提交事务

通过`connection.setAutoCommit(false)`方法取消自动提交事务，这条语句也相当于开启事务

使用`connection.rollback()`来进行回滚操作

使用`connection.commit()`来提交事务

```java
Connection connection = null;
PreparedStatement preparedStatement = null;
try {
    connection = JDBCUtils.getConnection();
    // 取消自动提交（开启事务）
    connection.setAutoCommit(false);
    String sql1 = "update test01 set money = 1900 where uName = 'origin'";
    String sql2 = "update test01 set money = 2100 where uName = 'jack'";
    preparedStatement = connection.prepareStatement(sql1);
    preparedStatement.executeUpdate();
    // 模拟异常
    int a = 1 / 0;
    preparedStatement = connection.prepareStatement(sql2);
    preparedStatement.executeUpdate();
	// 提交事务
    connection.commit();
} catch (Exception e) {
    System.out.println("发生异常，回滚");
    try {
        // 回滚至connection.setAutoCommit(false);处
        connection.rollback();
    } catch (SQLException ex) {
        ex.printStackTrace();
    }
    e.printStackTrace();
} finally {
    JDBCUtils.close(null, preparedStatement, connection);
}
```





# 批处理

批处理相比于一条一条地执行sql有更高地效率

```java
preparedStatement = connection.prepareStatement(sql);
for (int i = 1; i <= 15000; i++) {
    preparedStatement.setString(1, "jack" + i);
    preparedStatement.setInt(2, 20000);
   // 每设置号一条sql语句，就将其加入batch
    preparedStatement.addBatch();
    // 每次发送5000条数据
    if ((i % 5000 ) == 0) {
    	//执行batch中的sql
        preparedStatement.executeBatch();
        // 将batch清空
        preparedStatement.clearBatch();
    }
}
```
注意：如果想要使用批处理功能，需要在url中加入`rewriteBatchedStatements=true`





# 数据库连接池

传统的获取Connection的方式，不能控制创建的连接数量，如果连接次数较多就会导致服务器崩溃。



**连接池原理**
连接池中有许多已经连接上mysql的`Connection`，在java程序中，每次从连接池中取出一个`Connection`使用，使用完之后取消对该Connection的引用（只是取消java对`Connection`的引用，并没有关闭`Connection`与MySQL的连接）。同时，连接池中的`Connection`数量有限，当所有的`Connection`都被使用时，如果仍然需要获取`Connection`，则该请求会进入等待队列。

连接池在java中为一个接口java.sql.DataSourse，该接口由第三方提供实现类，常用的第三方连接池有C3P0和Druid。


## C3P0

使用C3P0前，需要引入jar包
```java
// 将配置信息传给连接池
comboPooledDataSource.setUser(user);
comboPooledDataSource.setPassword(password);
comboPooledDataSource.setJdbcUrl(url);
comboPooledDataSource.setDriverClass(driver);
// 设置连接池内Connection的初始数量
comboPooledDataSource.setInitialPoolSize(10);
// 设置连接池内Connection的最大数量
comboPooledDataSource.setMaxPoolSize(50);
for (int i = 0; i < 5000; i++) {
	// 获取连接方法
    Connection connection = comboPooledDataSource.getConnection();
    // 注意在连接池里的close方法只是取消了对该connection的引用，而并没有关闭与mysql的连接
    connection.close();
}
```






除了在java程序中直接设置配置信息外，C3P0设计者提供了一个C3P0-config.xml文件让我们设置连接数据库的各种参数信息
该xml文件需要复制到src目录下

```xml
<c3p0-config>
    <!-- 数据源（连接池）名称,区分不同的连接池 -->
  <named-config name="origin_source">
<!-- 驱动类 -->
  <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
  <!-- url，这里和mysql5版本的url不一样，，，找了好久才找到正确写法，&需要用&amp;代替-->
  	<property name="jdbcUrl">jdbc:mysql://127.0.0.1:13306/test?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true</property>
  <!-- 用户名 -->
  		<property name="user">root</property>
  		<!-- 密码 -->
  	<property name="password">origin</property>
  	<!-- 每次扩容的连接数-->
    <property name="acquireIncrement">5</property>
    <!-- 初始的连接数 -->
    <property name="initialPoolSize">10</property>
    <!-- 最小连接数 -->
    <property name="minPoolSize">5</property>
   <!-- 最大连接数 -->
    <property name="maxPoolSize">50</property>

	<!-- 可连接的最多的命令对象数 -->
    <property name="maxStatements">5</property> 
    
    <!-- 每个连接对象可连接的最多的命令对象数 -->
    <property name="maxStatementsPerConnection">2</property>
  </named-config>
</c3p0-config>
```



在xml文件中配置好这些信息并将该xml文件放入src目录下后，就可以直接获取连接了
```java
ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("origin");
Connection connection = comboPooledDataSource.getConnection();
```





## Druid
使用前也需要引入jar包，并需要将properties配置文件拷贝到src目录下

```properties
#key=value
driverClassName=com.mysql.cj.jdbc.Driver
#注意druid的url中&直接写即可，而在C3P0中&需要用&amp;代替
url=jdbc:mysql://127.0.0.1:13306/test?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true&rewriteBatchedStatements=true
username=root
password=origin
#initial connection Size
initialSize=10
#min idle connection size
minIdle=5
#max active connection size
maxActive=50
#max wait time (5000 mil seconds)即在等待队列中的最大等待时间
maxWait=5000
```





通过`DruidDataSourceFactory`类创建datasourse对象，读取配置文件，创建`Connection`对象

```java
Properties properties = new Properties();
properties.load(new FileInputStream("JDBC\\src\\druid.properties"));
DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
Connection connection = dataSource.getConnection();	
```





**注意**

C3P0中的connection全类名为：com.mchange.v2.c3p0.impl.NewProxyConnection

Druid中的connection全类名为：class com.alibaba.druid.pool.DruidPooledConnection

mysql的原生connection全类名为：class com.mysql.cj.jdbc.ConnectionImpl

这是三个不同的connection，他们的close方法实现也不同，mysql的close方法是关闭connection与mysql的连接，而C3P0和Druid的close方法只是取消java程序对connection的引用，而并没有关闭与mysql的连接



# Druid工具类



​	将Druid封装成工具类

```java
public class JDBCUtilsByDruid {
    private static DataSource dataSource;

    static {
        try {
            Properties properties = new Properties();
            /*InputStream stream =     JDBCUtilsByDruid.class.getClassLoader().getResourceAsStream("druid.properties");*/
            properties.load(new FileInputStream("JDBC\\src\\druid.properties"));
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }

    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        try {
            // 注意这里的close方法依然只是取消对connection的引用，而并没有关闭连接
            if (resultSet != null) resultSet.close();
            if (statement != null) statement.close();
            if (connection != null) connection.close();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```







# Apache-dbutils

**引入**

1. 当connection关闭连接后，resultSet就无法使用了，而通常我们希望得到resultSet后，就立即关闭连接，以便其他的程序连接mysql
2. resultSet获取数据不方便不直观



因此考虑创建一个类，该类的一个对象就对应表中的一行数据，将每一行的数据封装到对象中，并加入arrayList，这样，就不需要resultSet来获取数据了，connection可以及时关闭，同时可以随时通过对象集合获取数据。

对应表中每一行数据的类一般称作JavaBean或domain





Apache提供的dbutils工具帮我们封装了上述操作

先将comoon-dbutils.jar包引入

```java
Connection connection = JDBCUtilsByDruid.getConnection();
QueryRunner queryRunner = new QueryRunner();
String sql = "SELECT * FROM test01";
// queryRunner对象的query方法用以执行sql，将每一行数据封装成一个对象存入集合，返回结果集合，如果
// sql语句中有占位符?，那么query方法还可以传入可变参数，表示占位符的值
// new BeanListHandler<>(Person.class)使用了反射机制，获取Person类的属性，然后进行封装
// 注意这里的Person中的每个属性名字要和表中的数据一一对应
List<Person> personList = queryRunner.query(connection, sql, new BeanListHandler<>(Person.class));
System.out.println(personList);
// 在query方法中会自动关闭resultSet和prepareStatement
JDBCUtilsByDruid.close(null, null, connection);
```



query中的第三个参数`Handler`针对不同的查询结果有不同

如果查询的结果需要返回一个集合，就用`BeanListHandler`

如果查询的结果只是单行数据，即一个对象，那么用`BeanHandler`

如果查询的结果是单行单列，即一个Object对象，那么使用`ScalarHandler`



使用update方法来执行dml语句，返回受影响的行数

```java
String sql = "UPDATE test01 SET uName = ? WHERE uName = ?";
int affectedRow = queryRunner.update(connection, sql, "petter", "jack3");
```







**query方法**

```java
public <T> T query(Connection conn, String sql, ResultSetHandler<T> rsh, Object... params) throws SQLException {
    PreparedStatement stmt = null;
    ResultSet rs = null;
    Object result = null;// 最后result会转为一个集合返回

    try {
        // 得到prepareStatement
        stmt = this.prepareStatement(conn, sql);
        // 设置sql中的占位符
        this.fillStatement(stmt, params);
        // 执行excuteQuery方法返回resultSet
        rs = this.wrap(stmt.executeQuery());
        // 将resultSet处理成对象集合arrayList并赋给result，这里封装对象用到了反射机制
        result = rsh.handle(rs);
    } catch (SQLException var33) {
        this.rethrow(var33, sql, params);
    } finally {
        try {
            // 关闭resultSet
            this.close(rs);
        } finally {
            // 关闭prepareStatement
            this.close((Statement)stmt);
        }
    }

    return result;
}
```



可以看出query方法底层其实就是将resultSet中的数据封装成对象集合并返回，同时帮我们关闭了statement和resultSet









# BasicDao

与表中的每一行记录对应的类叫做javaBean或domain，Dao表示完成对表的操作的类，例如GoodsDao专门完成对Goods表的增删改查操作，不同的Dao完成对不同表的增删改查操作，由于不同表之间总会有一些相同的操作，因此设置一个BasicDao完成这些共同的操作，其他的Dao继承BasicDao，在BasicDao的基础上添加自己特有的操作。

Dao全称是 data access object，即数据访问对象，或者说访问数据的对象



**BasicDao**

```java
public class BasicDao<T> {
    private QueryRunner qr = new QueryRunner();

    // dml
    public int update(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return qr.update(connection, sql, parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 查询多行数据
    public List<T> queryMultiply(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return qr.query(connection, sql, new BeanListHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 查询单行数据
    public T querySingle(String sql, Class<T> clazz, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return qr.query(connection, sql, new BeanHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }

    // 查询单行单列
    public Object queryScalar(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JDBCUtilsByDruid.getConnection();
            return qr.query(connection, sql, new ScalarHandler(), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtilsByDruid.close(null, null, connection);
        }
    }
}
```





**PersonDao**

```java
public class PersonDao extends BasicDao<Person> {
}
```





这样就可以直接通过PersonDao对象直接一条语句执行对person表的sql了

```java
PersonDao personDao = new PersonDao();
List<Person> people = personDao.queryMultiply("select * from test01", Person.class);
```

