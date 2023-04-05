# hello,JavaScript

1. JavaScript是一种运行在客户端的脚本语言，不需要编译，直接由js解释器逐行解释并执行

2. 浏览器分为两部分，**渲染引擎**和**JS引擎**

   渲染引擎用来解析HTML和CSS

   JS引擎，即JS解释器，用来读取并执行JavaScript代码

3. JS引擎会逐行解释每一句代码（转为机器语言）然后执行，因此称为脚本语言







# JS书写位置



## 行内式

直接写到元素内部

```css
<input type="button" value="按钮" onclick="alert('hello,JavaScript')">
```



## 内嵌式

写在head标签中

```css
<head>
    <script>
        alert('hello,JavaScript');
    </script>
</head>
```



## 外部js

将js代码写在js文件中，然后在html中引入

```html
<script src="my.js"></script>
```



# 输入输出语句

```javascript
prompt('请输入');//输入
alert('输出');//输出警示框
console.log('控制台可见');//在控制台中打印
```



# 变量



 ## 声明变量

使用var关键字即可

```javascript
var age = 18;
```



## 变量调用

```javascript
var name = prompt('input your name');
alert(name);
```





# 数据类型



 ## 简单数据类型

- number
  - `Infinity `无穷大
  - `Infinity ` 无穷小
  - `NaN` 非数值（例如用字符串与数字进行运算，就会输出NaN）
    - `isNaN()`方法判断是否是NaN，返回true false
- Boolean
  - true和false在参与运算时当1和0来看待
- String
  - 单引号和双引号都可以
- Undefined
  - 当一个变量声明但未初始化或未赋值时，就是Undefined类型
- Null
  - null类型参与运算时当作0看待



## 获取数据类型

当数据类型为null时，typeof输出的是object

```javascript
var v = 'string';
alert(typeof(v));
```







# 数组



## 创建数组

两种方式，且js中的数组无需指定类型和大小，一个数组可以放任意个不同类型的元素

```javascript
var array = new Array();
var array = [];
```



## 常用方法

- push() 在末尾添加一个或多个元素，返回数组当前长度
- unshift() 在开头添加一个或多个元素，返回数组当前长度
- pop() 删除最后一个元素，返回删除的元素
- shift() 删除第一个元素，返回删除的元素
- indexOf() 获取指定元素第一次出现的索引，找不到返回-1
- lastindexOf() 获取指定元素最后一次出现的索引，找不到返回-1
- join() 将数组转为字符串，可以传一个分隔符参数，这样可以用该分隔符来分割每个元素



# 函数



 ## 定义函数

关键字function，形参不用写类型

函数没有return时返回的是undefined

```javascript
function hello(name) {
    alert("my name is " + name);
}
```



另一种定义函数的方式

这里的fun是一个变量名，而不是函数名，即fun变量里存的是一个函数

```javascript
var fun = function(num) {
    alert(num);
}
fun(10);
```





## arguments

所有的函数都内置了一个arguments对象，arguments对象中**存储了传递的所有实参**[^1]

arguments属于伪数组具有数组的一些属性，但是没有真正数组的一些方法

```javascript
function a() {
    alert(arguments);
    alert(arguments.length);
    alert(arguments[2]);
}
a(1, 2, 3, 4, 5);
```







# 作用域

在函数内部没有声明，直接赋值的变量，属于全局变量[^2]





# 预解析

js引擎运行js分为两步，预解析以及代码执行

**预解析**：把所有var和function提升到当前作用域的最前面

- 提升的是var的声明，而不提升赋值
- 提升的是函数的声明，而不是函数调用

**代码执行**：按顺序从上往下执行



# 对象



 ## 创建对象

有三种方法

```javascript
//第一种方式
var person = {
    name: 'origin',
    age: 20,
    sex: 'man',
    sayHi: function() {
        alert('hello,world');
    }
}

//第二种方式
var person = new Object();
person.name = 'origin';
person.age = 20;
person.sayHi = function() {
    console.log('hi');
}
```



上面的两种方法一次只能创建一个对象

利用构造函数创建对象

```javascript
function person(name, age) {
    this.name = name;
    this.age = age;
}
//new对象
var origin = new person('origin', 20);
var jack = new person('jack', 30);
```





## 调用属性和方法

调用属性有两种语法

```javascript
alert(person.name);
alert(person['name']);
person.sayHi();
```





## for in遍历对象属性

类似于增强for循环

```javascript
function person(name, age) {
    this.name = name;
    this.age = age;
}
var origin = new person('origin', 20);
for (var k in origin) {
    //这里的k取的是orign对象里的属性名，即name和age
    //要取得属性值需要使用origin[k]
    console.log(origin[k]);
}
```



# 日期

```javascript
var date1 = new Date();//当前时间
var date2 = +new Date();//当前时间戳
var date3 = Date.now();//当前时间戳
```



以xxxx年xx月xx日星期x的方式返回时间

```javascript
function getDate() {
    var date = new Date();
    var year = date.getFullYear();
    var month = date.getMonth() + 1;
    var dates = date.getDate();
    var array = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
    var day = date.getDay();
    return year + '年' + month + '月' + dates + '日' + array[day];
}
```



[^1]: js中允许实参和形参个数不一致
[^2]: js允许不声明，直接使用变量

