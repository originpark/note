



# jQuery概述

jQuery是一个JavaScript库，把DOM操作做了封装。



## 等待DOM加载

等待DOM加载完成后再调用该函数

```html
<script>
    $(function() {

    })
</script>
```



## jQuery顶级对象

jQuery中的顶级对象即jQuery，大多数情况下我们用$代替，即$和jQuery是等价的，可以直接替换。





## DOM对象和jQuery对象

DOM对象即用原生js获取的对象

jQuery对象即用jQuery获取的对象

**jQuery对象只能使用jQuery方法，DOM对象只能使用原生js方法**



## DOM对象和jQuery对象相互转换



直接获取jQuery对象

```html
<body>
    <div class="box"></div>
    <script>
        $(function() {
            $('div');//获取div
        })
    </script>
</body>
```



将DOM对象转为jQuery对象

```html
<body>
    <div class="box"></div>
    <script>
        var box = document.querySelector('.box');
        $(box);
    </script>
</body>
```



将jQuery对象转换为DOM对象

```javascript
var a = $(box)[0];
var b = $(box).get(0);
```







# jQuery常用API



## jQuery选择器



基本语法如下

```
$("css选择器")
```



### 隐式迭代

当我们选取的元素有多个时，操作时会自动进行循环操作，例如下面的操作会给ul中的每一个li都设置`backgroundcolor`

```
$('ul li').css('background', 'pink');
```



### 筛选选择器

```html
<script>
    $('ul li:first');//第一个
    $('ul li:last');//最后一个
    $('ul li:eq(index)');//指定索引
    $('ul li:odd');//奇数索引
    $('ul li:even');//偶数索引
</script>
```



### 筛选方法

```html
<script>
    $('.son').parent();//最近一级父级
    $('.parent').children('div');//指定的所有最近一级子元素
    $('parent').find('div');//指定的所有子元素
    $('.box').siblings('li');//指定的左右兄弟元素
    $('ul li').eq(2);//指定索引
</script>
```



注：筛选方法和筛选选择器可以组合使用，例如

```javascript
$('.parent').children('div:first');//指定的第一个最近子元素
```



### 常用方法

- $('.box').css('属性', '值');//设置css
- $('.box').show();
- $('.box').hide();
- $(this).index();//得到当前元素的索引



### 链式编程

```html
$(this).css('backgroundColor','pink').siblings().css('backgroundColor', '');
```





## jQuery修改样式

- $('.box').css('width');//返回属性值
- $('.box').css('width', '100px');//修改属性值
- $('.box').addClass('current');//添加类（不删除原有类）
- $('.box').remove('current');//移除类
- $('.box').toggleClass('current');//切换类（没有就加上，有就去掉）



以对象的方式修改css

```html
<script>
    $(function() {
        $('ul').css({
            width: '100px',
            height: '100px',
            backgroundColor: 'pink'
        })
    })
</script>
```





## jQuery效果



### 显示&隐藏

```javascript
show(speed, easing, fn);
hide(speed, easing, fn);
//speed: 'slow', 'normal', 'fast',也可以是具体毫秒数
//easing: 'swing', 'linear'
//fu: 回调函数 
```



### 滑动

```
$('.box').slideUp(speed, easing, fn);
$('.box').slideDown(speed, easing, fn);
$('.box').slideToggle(speed, easing, fn);
```



### 事件切换

```
$('.box').hover(function() {}, function() {});
//鼠标经过时调用第一个函数，鼠标离开时调用第二个函数，括号中也可以只写一个函数，表示鼠标经过和离开时都会调用该函数
```



### 停止排队

```
stop();//停止上一个动画
```





### 淡入淡出

```
fadeIn(speed, easing, fn);
fadeOut(speed, easing, fn);
fadeToggle(speed, easing, fn);
fadeTo(speed, opacity, easing, fn);//渐进方式调整不透明度，opacity为0~1间的数，speed和										//opacity必写
```



### 自定义动画

animate(params, speed, easing, fn);

这里的params是一个对象，例如

```html
<script>
    $(function() {
        $('button').click(function() {
            $('.box').animate({
                left: '200px',
                top: '200px'
            })
        })
    })
</script>
```





## jQuery属性操作



### 设置或获取元素固有属性

```
element.prop('href');//获取属性值
element.prop('class', 'box');//设置属性值

```



### 设置或获取元素自定义属性

```
element.attr('index');//获取属性值
 element.attr('index', 4);//设置属性值
```





## jQuery内容文本值

element.html();//获取元素内容

element.html("content");//设置元素内容

element.text();//获取/设置元素文本内容

element.val();//等价于原生中的value



## jQuery元素操作



### 遍历元素

```html
<script>
    $('.box').each(function(index, domEle) {
        console.log(index); //索引
        console.log(domEle); //索引对应的DOM元素
    })
    //另一种写法：
    $.each($('.box'), function() {});
</script>
```



### 创建&添加&删除元素

```html
<script>
    //创建元素
    var li = $('<li></li>');
    //添加元素——内部添加
    $('ul').append(li);//放在内容的最后
    $('ul').prepend(li);//放在元素最前面
    //添加元素-外部添加
    element.after(li);//放在之后
    element.before(li);//放在之前
    //删除元素
    element.remove();//删除element 
    element.empty();//删除element的所有子元素
</script>
```





## jQuery尺寸，位置操作



### 尺寸

element.width();//获取尺寸，不包括边距和边框等

element.width(300);//修改尺寸

element.innerHeight();//获取尺寸 + padding

element.outerHeight();//获取尺寸 + padding + border，括号里加上参数`true`则可以再加上marign





### 位置

offset() 获取**相对于文档**的偏移，返回的是一个对象，包含top和left属性，可以通过element.offset().left/top 获取top或left



修改位置

```
element.offset({
	left: 100.
	top: 100
});
```



position() 返回相对于带有定位的父级的偏移，如没有父亲或者没有定位则以文档为主，position方法只能获取不能设置



e.scrollTop() 获取元素被卷去的头部，括号里填参数可以设置元素被卷去的头部







## jQuery事件



### 事件注册

单个注册事件基本与原生方式相同，例如e.click(function() {});



### 事件处理

使用on给一个元素添加事件，on中传入一个对象，每个事件函数为对象中的属性

```html
<body>
    <div class="box"></div>
    <script>
        $('.box').on({
            mouseenter: function() {
                $(this).css('backgroundColor', 'pink');
            },
            mouseleave: function() {
                $(this).css('backgroundColor', '');
            }
        })
    </script>
</body>
```



也可以给多个事件添加相同的事件处理函数

```html
<body>
    <div class="box"></div>
    <script>
        $('.box').on('mouseenter mouseleave', function() {
            $('.box').toggleClass('current');
        })
    </script>
</body>
```



事件委派（原理见web api）

```html
<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
    </ul>
    <script>
        //给ul绑定事件，但是触发对象是li，通过冒泡，就实现了只绑定一个事件实现所有li的点击事件
        $('ul').on('click', 'li', function() {
            alert('hello,world!');
        })
    </script>
</body>

```



e.one(function() {});//只会执行一次的事件



#### 解绑事件

element.off();//解绑elemnet的所有事件

element.off(event);//解绑element的指定事件



#### 自动触发事件

e.event();

e.trigger('event');

e.triggerHandler('event');//不会触发元素的默认行为



### 事件对象

event.stopPropagation();//阻止冒泡





## jQuery其他方法

$.extend(true, target, basic);//把basic拷贝给target,true代表深拷贝