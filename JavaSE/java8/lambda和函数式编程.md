

# lambda表达式



函数式接口: 只有一个抽象方法的接口

lambda表达式的返回值: 函数式接口的实现类



实例:

有如下函数式接口

```java
public interface MyInterface {
    void m1();
}
```

使用lambda表达式创建该接口的实现类实例

```java
@Test
void test() {
    MyInterface i = () -> {
      System.out.println("hello, world");  
    };
}
```



# java内置函数式接口



consumer:有一个参数,无返回值

```java
@Test
void m2() {
    Consumer<String> con = (s) -> {
        System.out.println(s);
    };
    con.accept("哈哈哈");
}
```





# 方法引用

比如说一个consumer实现对象`Consumer<String> con1 = s -> System.out.println(s);`,其lambda方法体是`System.out.println(s)`,发现这个方法体所需要实现的打印功能已经被System.out下的println函数实现了,此时就可以使用方法引用 ,即引用的方法和参数和返回值与lambda方法体的参数和返回值相同

```java
@Test
void m2() {
    Consumer<String> con1 = s -> System.out.println(s);println;//方法引用
    con1.accept("哈哈哈");

    User user = new User();
    user.setNickName("jack");
    Supplier<String> sup1 = () -> user.getNickName();
    sup1 = user::getNickName;//方法引用
}
```

