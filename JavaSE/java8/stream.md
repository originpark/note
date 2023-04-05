# stream概述

用于操作数据源的数据渠道,关注的是对数据的运算



# stream执行步骤

1. 获取stream对象
2. 一系列的中间操作
3. 终止操作



# stream的实例化

通过集合

```java
Stream<String> stream = list.stream(); // 顺序流
Stream<String> stream1 = list.parallelStream(); // 并行流
```



通过数组

```java
IntStream stream2 = Arrays.stream(arr);
```



通过Stream的of方法

```java
Stream<Integer> stream3 = Stream.of(1, 2, 3, 4, 5);
```



# stream的中间操作



## 筛选与切片

```java
list.stream().filter(p -> p.getId() > 3).forEach(System.out::println);// 过滤
list.stream().limit(4).forEach(System.out::println);// 限制个数为前4个
list.stream().skip(4).forEach(System.out::println);// 跳过前4个
```



## 映射

与上面的不同的是,映射后的stream类型可能会变化

```java
// 将list中所有人的name转为大写
list.stream().map(p -> p.getName().toUpperCase()).forEach(System.out::println);
```



map方法,将list的每个元素映射为stream对象后,返回`Stream<Stream>`

```java
Stream<Stream> streamStream = list.stream().map(Test::String2Stream);
```



faltMap方法,将list的每个元素映射为stream对象后,返回`Stream`,即将传入的stream的内部内容取出

```java
Stream stream = list.stream().flatMap(Test::String2Stream);
```





## 排序

```java
// 自然排序
list.stream().sorted().forEach(System.out::println);
// 定制排序,传入comparable
list.stream().sorted((e1, e2) -> Integer.compare(e2.getId(), e1.getId())).forEach(System.out::println);
```







# stream终止操作



## 匹配与查找

```java
// 所有元素满足匹配条件则返回true
boolean b = list.stream().allMatch(p -> p.getId() < 10);
// 任意一个元素满足匹配条件则返回true
boolean b = list.stream().anyMatch(p -> p.getId() < 5);
// 没有任何元素满足条件则返回true
boolean b = list.stream().noneMatch(p -> p.getId() < 5);
```



```java
findFirat(); // 返回第一个元素
findAny(); // 返回任意一个元素
count(); // 返回个数
max(Comparator c); // 返回流中最大值
min(Comparator c); // 返回流中最小值
foreach(Consumer c) // 内部迭代
```



## 归约

将流中的元素反复结合起来,得到一个值

```java
Integer sum = list.stream().reduce(0, Integer::sum);
```

> 0表示初始值是0, Integer::sum表示用累加的方式结合求总和



## 收集

将stream操作得到的流转为集合等容器,这里collect方法内调用的是Collectors的静态方法

```java
List<Integer> collect = list.stream().filter(n -> n > 0).collect(Collectors.toList());
```

