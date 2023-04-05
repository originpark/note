```java
 String str = new String("jack");
// 创建optional实例,传入一个非空对象
Optional<String> stringOptional = Optional.of(str);
// 创建空的optional类实例
Optional<Object> emptyOptional = Optional.empty();
// 创建optional实例, 可以传入空值
Optional<Object> o = Optional.ofNullable(null);
// orElse方法, 如果optional维护的对象为null,则返回传入的对象,如果不为空,则直接返回原对象
Object o1 = o.orElse(new String("error"));
// get方法,获取optional对象所维护的数据,但要确保非空
Object o2 = o.get();
```

