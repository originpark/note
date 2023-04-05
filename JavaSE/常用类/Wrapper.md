# 包装类

包装类即Wrapper类，分别是八个基本数据类型所对应的类

其中`Byte`，`Integer`，`Long`，`Float`，`Double`，`Short`继承了`Number`类



## 包装类和基本数据类型间的转换

装箱：基本数据类型转为包装类

拆箱：包装类转为基本数据类型



下面演示装箱和拆箱

```java
int n = 100;
//手动装箱
Integer integer = new Integer(n);
Integer integer1 = Integer.valueOf(100);
//手动拆箱
int value = integer.intValue();
```



JDK5以后已经实现了自动装箱和拆箱 

自动装箱调用的是`valueOf`方法

自动拆箱调用的是`intValue`方法

