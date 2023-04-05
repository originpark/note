# AJAX概述

AJAX翻译为 异步 JavaScript 和 XML ，是一种浏览器通过js异步发起请求，局部更新页面的技术







# 原生js的AJAX请求

```javascript
//创建XMLHttpRequest对象
let xmlHttpRequest = new XMLHttpRequest();
//open方法来规定该请求(请求方式，请求地址，是否异步)
xmlHttpRequest.open("GET", "http://localhost:8080/Book/ajaxServlet?action=jsAjax", true);
//XMLHttpRequest对象的onreadystatechange属性为请求接收到应答时所执行的函数，该函数的定义要
//在send方法调用之前
xmlHttpRequest.onreadystatechange = function () {
    //XMLHttpRequest对象的readyState属性保存了XMLHttpRequest对象的状态，
    //4代表请求已完成且响应已就绪，status属性表示响应码
    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
        //responseText属性以js字符串的形式返回服务器响应过来的数据,通常是JSON字符串
        //即resp.getWriter().write(objJsonString)
        let objJsonString = xmlHttpRequest.responseText;
        //将JSON字符串转为JOSN对象
        let obj = JSON.parse(objJsonString);
        //获取JSON对象的每个value，填入box盒子中，从而实现局部更新页面内容
        document.getElementById("box").innerHTML = Obj.name + " " + Obj.age;
    }
}
//send方法将get请求发送到服务器
xmlHttpRequest.send();
```



**ajax特性理解**

局部更新：以前每当我们发送一个请求，地址栏的地址会发生变化，页面也会跳转，而ajax请求地址栏不会发生变化，所以页面也不会变化，但是该请求可以通过得到的响应来局部更新页面上的内容

异步请求：如果是同步请求，那么send方法调用之后，后面的代码必须等收到响应并调用onreadystatechange函数之后才会继续执行，这样如果该请求比较耗时的话，就会影响用户与其他功能的交互，而如果是异步请求，则send方法的调用不会影响后续代码的立即执行，因此就算该请求比较耗时也不会影响用户与其他功能的交互





# JQuery中的AJAX



## ajax

```javascript
let box = document.getElementById("info");
$("#b").click(function () {
    $.ajax({//发起ajax请求
        url: "http://localhost:8080/Book/ajaxServlet",//要请求的地址
        data: "action=jsAjax",//请求参数
        type: "GET",//请求类型
        success: function (data) {//响应成功后的回调函数，这里的data是响应过来的数据
            let obj = JSON.parse(data);
            box.innerHTML = obj.name + " " + obj.age;
        },
        //dataType表示响应过来的数据类型，有text纯文本，xml，json对象
        //text表示响应过来的数据是字符串，json表示响应过来的数据是json对象
        //因此使用json则可以直接使用而不需要parse方法来转换了
        dataType: "text",
    })
})
```



## get和post

get和post方法就是在ajax方法的基础上进一步区分了get请求和post请求

例如get方法，$.get(url, data, success, dataType)

post方法，$.post(url, data, success, dataType)

```javascript
$.get("http://localhost:8080/Book/ajaxServlet", "action=jsAjax", function (data) {
    let obj = JSON.parse(data);
    box.innerHTML = obj.name + " " + obj.age;
}, "text");
```





## getJson

getJson方法就是在get方法的基础上确定dataType的值为json

$.getJSON(url, data, success)

```javascript
$.getJSON("http://localhost:8080/Book/ajaxServlet", "action=jsAjax",function (data) {
    box.innerHTML = data.name + " " + data.age;
})
```



## serialize

serialize方法可以获取表单元素的所有表单项的值，并以key=value&key=value字符串的形式返回

```javascript
$("#form").serialize();
```

