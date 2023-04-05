# StringBuffer&StringBuilder

1. String中保存的是字符串常量，每次改变的是地址，在进行字符串拼接时，总会构建一个新的String对象，耗时且浪费空间
2. StringBuffer&StringBuilder中保存的是字符串变量，里面的值可以更改，每次改变的是内容
3. StringBuffer允许多线程中使用，StringBuilder只允许单线程使用，但是StringBuilder的效率更高



StringBuilder追加字符串并返回this

```java
StringBuilder builder = new StringBuilder();
builder.append("hello,world.");
```



将String转为StringBuilder

```java
StringBuilder builder = new StringBuilder("hello,world.");
builder.append("orign");
```



将StringBuilder转为String

```java
String str = builder.toString();
String str01 = new String(builder);
```



