# JSON概述

JSON是一种轻量级的数据交换格式

轻量级指的是跟xml做对比

数据交换指的是客户端和服务器之间业务数据的传递格式





# JSON的定义

在JavaScript中，每个JSON由一个大括号包裹，其内有任意个键值对，每个键值对的key用引号引起来，key与value用冒号间隔，每个键值对之间用逗号间隔

```jsp
<script type="text/javascript">
    let JSONObj = {
        "key1": 100,
        "key2": "200",
        "key3": true,
        // value也可以是一个JSON对象
        "key4": {
            "key4_1": [1, 3, 5],
            "key4_2": [2, 4, 6],
        }
    }
</script>
```





# JSON访问

JSON是一个对象，将JSON中的每个key看成对象中的属性,通过key.value的方式访问JSON

```javascript
let key1 = JSONObj.key1;

for (let i = 0; i < JSONObj.key4.key4_1.length; i++) {
    let value = JSONObj.key4.key4_1[i];
}
```





# JSON两个常用方法

JSON的存在有两种形式，一种是以对象的形式存在，我们通常使用JSON对象来操作JSON中的数据

一种是以JSON字符串的形式存在，我们在客户端和服务器之间进行数据交换的时候使用字符串的形式

`JSON.stringify(json对象)` 将JSON对象转换为JSON字符串

`JSON.parse(json字符串)` 将JSON字符串转换为JSON对象





# java中的JSON操作

在java中获取JSON需要使用谷歌提供的工具类gson，导入相应jar包



## javaBean与JSON字符串的转化

```java
// 创建gson对象实例
Gson gson = new Gson();
Person person = new Person("jack", 12);
// toJson方法可以将java对象转为JSON字符串
String jsonString = gson.toJson(person);
// 将json字符串转为java对象
Person toPerson = gson.fromJson(jsonString, Person.class);
```





## 对象集合转与JSON的转化

```java
Gson gson = new Gson();
ArrayList<Person> people = new ArrayList<>();
people.add(new Person("1" ,1));
people.add(new Person("2" ,2));
String s = gson.toJson(people);
// 这里的PersonType是我们自己定义的一个空类，用以确定返回的类型
ArrayList<Person> list = gson.fromJson(s, new PersonType().getType());
```



我们自己定义的这个空类的泛型为我们想要返回的类型

```java
class PersonType extends TypeToken<ArrayList<Person>> {
}
```

