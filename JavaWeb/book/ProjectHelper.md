##### 生成测试框架

CTRL + shift + T 生成测试框架



##### js正则

js里面的正则不能用""包起来，而是用//包起来



##### js let变量

js现在一般用let代替var



##### js return false

js中return false 用于阻止事件对象的默认行为



##### js 判断checkbox是否勾选

js中判断checkbox是否被选中使用checkbox.prop('checked')，返回true或false



##### 项目使用base标签

使用base标签来解决请求转发时的路径跳转错误问题



##### 标签中的name属性

requst对象的getParameter方法传入的是标签中的name属性



##### 项目引入jar包

 项目中引入jar包的步骤，进入project structure ，先在libraries里面讲所有需要引入的jar包打包成一个lib

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220416150229196.png" alt="image-20220416150229196" style="zoom:50%; margin-left:0;" />



然后在modules中给相应项目添加lib

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220416150407318.png" alt="image-20220416150407318" style="zoom: 33%; margin-left: 0px;" />



最后在artifacts中点击fix



##### 项目中java源码中的路径

启动项目后，src目录中的根目录变成了tomcat下的bin目录，此时在创建流对象时路径就不能以项目名作为根

了，可以使用以下方法获取路径

```java
Properties properties = new Properties();
InputStream stream = JDBCUtilsByDruid.class.getClassLoader().getResourceAsStream("druid.properties");
// 原本这里load的是new FileInputStream("src//druid.properties");
properties.load(stream);
```





#####  在链接中设置action

```html
<a href="/servletDemo?action=method1"></a>
```



##### 动态设置action的值

令action=${param.key}，这里的key是提交时传过来的参数，这样action的值就是动态变化的



##### sql中查询返回的数字的数据类型

例如count，sum等函数，返回值其实是long类型，因此如果用Integer接收就会报ClassCastException，

解决办法是先使用Number接收，再调用Number的intValue方法转为value，例如：

```java
String sql = "SELECT COUNT(*) FROM books";//本句sql返回的是long类型
Number count = (Number) queryScalar(sql);//先将long转为Number
return count.intValue();//调用intValue方法
```



##### js中写el

如果js直接写在jsp页面中，那么则可以在js中使用el表达式



##### 获取请求的页面地址

请求头中有一个请求头叫Referer，该请求头保存了发起请求的页面



##### confirm确定框

js中arlet是弹出提示框，confirm是弹出确定框



##### js更改页面地址

js中使用location.href来修改当前的请求地址
