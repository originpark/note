# DOM

DOM译为文档对象模型，是一个处理html等语言的编程接口

- 文档`document` 一个页面就是一个文档
- 元素`element` 文档中的标签即元素
- 结点`node` 网页中的所有内容都是结点



## 获取元素



### 根据ID获取

`getElementById`传入id名的字符串，返回一个元素对象

由于是先有便签才能获取该便签，所以js代码要写在标签的后面

```html
<body>
    <div id="box"></div>
    <script>
        //document代表页面，这里也可以写其他的父元素
        var o = document.getElementById('box');
        console.dir(o);//console.dir可以打印对象的属性
    </script>
</body>
```



### 根据标签名获取

`getElementsByTagName`传入标签名的字符串，返回元素对象的一个伪数组

```javascript
var list = document.getElementsByTagName('li');
```



### 根据类名获取

`getElementByClassName`传入类名的字符串，返回元素对象的一个伪数组

```javascript
var boxs = document.getElementByClassName('box');
```



### 根据选择器获取

`querySelector`传入一个选择器名的字符串，返回该选择器代表的元素中的第一个元素

`querySelectorAll`返回的是该选择器代表的元素的集合

```javascript
var box = document.querySelector('#box');
var box = document.querySelectorAll('#box');
```



### 获取html标签

```javascript
var htmlEl = document.documentElement;
```





## 事件基础

- 事件源：即触发事件的对象
- 事件类型：事件触发的方式，例如点击，经过等
- 事件处理程序：通过函数赋值实现

```html
<body>
    <button id="b">button</button>
    <script>
        //得到事件源
        var b = document.getElementById('b');
        //用事件源.事件类型 = 事件处理函数
        b.onclick = function() {
            alert('click');
        }
    </script>
</body>
```



### 常见事件类型

- onclick 点击
- onfocus 光标定位
- onblur 失去光标定位
- onmouseover 鼠标经过
- onmouseout 鼠标离开







## 操作元素



### 改变元素内容

`innerText`表示元素内部的文本

```javascript
b.onclick = function() {//当b对象被点击时
    time.innerText = getDate();//time对象内的文本变成当前时间
}
```



`innerText`不识别html标签，而`innerHTML`可以识别html标签，例如

```javascript
box.innerHTML = '<strong>hello</strong>, world.';
```



可以直接改变元素的某个属性，但是只能得到内置属性，例如

```javascript
b1.onclick = function() {
            image.src = "../../images/1.jpg";//改变图片的src
        }
```



可以用getAttribute方法来获取元素属性,可以得到内置属性和自定义属性，如果自定义属性中有-的话，则要改为驼峰命名法

```javascript
console.log(box.getAttribute('type'));
```



可以用setAttribute方法来修改元素的属性,主要设置的是自定义属性,也可以用来新增一个自定义属性

```javascript
box.setAttribute('index', '1');
```



使用removeAttribute来删除元素的某个属性

```javascript
box.removeAttribute('name');
```





修改元素的样式属性，等价于给元素添加一个行内样式

```javascript
box.onclick = function() {
    this.style.backgroundColor = 'purple';
}
```



也可以通过改变元素的类名来变换样式

```javascript
box.onclick = function() {
    this.className = 'change';
}
```



获取自定义属性，dataset是一个集合，里面存放了所有以data开头[^3]的自定义属性

```javascript
console.log(div.dataset.index);//获取div中的自定义属性index的值
```



[^3]: 自定义属性规定以data-开头



## 结点操作



结点操作能够更简单地获取元素

页面中所有的内容都是结点

结点至少拥有nodeType，nodeName，nodeValue三个属性

nodeType分为元素结点，属性结点和文本结点，其nodeType值分别为1，2，3   



### 结点层级



通过`parentNode`获取直接父级结点

```html
<body>
    <div class="parent">
        <div class="child"></div>
    </div>
    <script>
        var child = document.querySelector('.child');
        var parent = child.parentNode;
        console.log(parent);
    </script>
</body>
```



通过`childNodes`得到所有的子结点，包括元素结点，属性结点和文本结点，例如下例中得到了ul的九个子结点，包括四个元素结点li和五个文本结点（换行符）

如果只想得到其中的元素结点li的话，可以利用结点类型nodeType来筛选，元素结点的nodeType值为1，而文本结点nodeType值为3

```html
<body>
    <ul>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
    </ul>
    <script>
        var ul = document.querySelector('ul');
        console.log(ul.childNodes);
    </script>
</body>
```



用`childNodes`获取子元素结点非常麻烦，使用`children`可以直接获取所有的子元素结点

children是一个伪数组，可以访问指定下标的子元素结点

```javascript
console.log(ul.children);
console.log(ul.children[1]);
```



- `firstChild` 获取第一个子结点（包括元素结点，文本结点和属性结点）
- `lastChild` 获取最后一个子结点（包括元素结点，文本结点和属性结点）
- `firstElementChild` 获取第一个字元素结点
- `lastElementChild` 获取最后一个子元素结点





通过`nextSilbing`获取下一个兄弟结点，包括元素结点，文本结点和属性结点，例如这里获取的s是一个文本结点（换行符）

```html
<body>
    <div></div>
    <span></span>
    <script>
        var d = document.querySelector('div');
        var s = d.nextSibling;
        console.log(s);
    </script>
</body>
```



- `nextElementSibling` 获取下一个兄弟元素结点
- `previousElemnetSibling` 获取上一个兄弟元素结点





### 创建结点

使用`createElement`来创建一个结点，然后通过`appendChild`将新创建的结点追加到指定元素的子元素最后

例如此处在ul中加入一个li

```html
<body>
    <ul></ul>
    <script>
        var ul = document.querySelector('ul');
        var newLi = document.createElement('li');
        ul.appendChild(newLi);
    </script>
</body>
```



使用`write`可以直接创建一个结点,但当文档流执行完毕后再执行write时，会发生页面重绘

```html
document.write('<div></div>');
```



`insertBefore`可以将创建的结点加入到指定元素的子元素的指定位置

```javascript
ul.insertBefore(newLi, ul.children[0]);//将newLi插入到ul的第一个孩子之前
```



### 删除结点

使用`removeChild`删除某个子元素

```html
<body>
    <ul>
        <li>123</li>
        <li>456</li>
        <li>789</li>
        <li>101112</li>
    </ul>
    <script>
        var ul = document.querySelector('ul');
        ul.removeChild(ul.children[0]);
    </script>
</body>
```





### 发布留言和删除留言案例

```html
<body>
    <textarea name="" id="input" cols="30" rows="10"></textarea>
    <button>发布</button>
    <ul></ul>
    <script>
        var button = document.querySelector('button');
        var text = document.querySelector('textarea');
        var ul = document.querySelector('ul');
        button.onclick = function() {//当发布按钮被点击时
            if (text.value == '') {//如果文本框value为空，则提出警示
                alert('输入不能为空');
            } else {
                //创建一个新的li结点
                var newLi = document.createElement('li');
                //将文本框的value值复制到新li中，并加入一个用来删除的a标签
                //Javascript:;表示该连接不跳转
                newLi.innerHTML = text.value + '<a href="javascript:;">删除</a>';
                //在ul的开头插入新li
                ul.insertBefore(newLi, ul.children[0]);
                //每次发布后，将文本框内容置空
                text.value = '';
                //每发布一个li，就要对该li中的删除链接a注册事件
            	var a = ul.children[0].querySelector('a');
            	a.onclick = function() {
                //删除该a的父结点li
                ul.removeChild(this.parentNode);
            	}
            }
        }
    </script>
</body>
```



### 克隆结点



使用通过`cloneNode`来复制一个结点

当参数为空或者为`false`时，为浅拷贝，只拷贝标签，不拷贝内容以及子结点

当参数为`true`时，为深拷贝，即复制标签和内容

```html
<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
    <script>
        var ul = document.querySelector('ul');
        var cNode = ul.children[0].cloneNode();
        ul.appendChild(cNode);
    </script>
</body>
```







## 事件进阶



### 注册事件

传统的注册事件方式为on开头的事件，例如onclick，这种方式注册的事件具有唯一性，及如果给某个事件同时写两个function，那么最终只会执行后写的function



**addEventListener**

代表一个事件监听器

第一个参数为事件的类型，第二个参数为事件处理程序

同一个元素的同一个事件可以添加多个监听器，即该事件可以有多个function

```html
<body>
    <button>input</button>
    <script>
        var b = document.querySelector('button');
        b.addEventListener('click', function() {
            alert('input');
        })
    </script>
</body>
```





### 删除事件

即解除注册的事件

传统方式，将元素的事件置为null

```html
<body>
    <div>input</div>
    <script>
        var b = document.querySelector('div');
        b.onclick = function() {
            alert(123);
            b.onclick = null;
        }
    </script>
</body>
```



**removeEventListener**

第一个参数为要删除的事件，第二个参数为要删除的事件处理理程序，因此这里注册事件不采用匿名函数

```html
<body>
    <div>input</div>
    <script>
        var b = document.querySelector('div');
        function fn() {
            alert('hello');
            b.removeEventListener('click', fn);
        }
        b.addEventListener('click', fn);
    </script>
</body>
```





### DOM事件流

事件流即从页面中接收事件的方式

DOM事件流分三个阶段，捕获阶段，当前目标阶段，冒泡阶段

例如

```html
<body>
    <div></div>
</body>
```

捕获阶段，事件先流入document，再流入html，再流入body，最后流入div时发现了事件，进入当前目标阶段，之后事件又逐级返回，从div到body到html到document，称为冒泡阶段



实际代码中，只会执行捕获阶段和冒泡阶段当中的一种，例如在使用`addEventListener`时可以设置第三个参数

第三个参数为false或默认时，表示冒泡传递，例如下面的代码当点击son盒子时会先弹出son，再弹出father

如果把son和father的第三个参数设置为true，则代表捕获传递，此时点击son盒子会先弹出father，再弹出son

```javascript
<body>
    <div class="father">
        <div class="son"></div>
    </div>
    <script>
        var father = document.querySelector('.father');
        var son = father.children[0];
        son.addEventListener('click', function() {
            alert('son')
        });
        father.addEventListener('click', function() {
            alert('father')
        });
    </script>
</body>

```





### 事件对象

事件对象写在事件处理函数的小括号中，其内包含了事件的状态等事件信息。

```javascript
box.onclick = function(event) {}
```





**常见属性和方法**

`target`方法可以返回触发事件的对象，例如这里给father注册了事件，当点击father，触发事件的是father，于是打印father，当点击son时，触发事件的是son，于是打印son

```html
<body>
    <div class="father">
        <div class="son"></div>
    </div>
    <script>
        var father = document.querySelector('.father');
        var son = father.children[0];
        father.onclick = function(e) {
            console.log(e.target);
        }
    </script>
</body>
```



- event.type 返回事件的类型

- event.preventDefault() 阻止默认事件，例如此处让a被点击时不跳转

  ```javascript
  a.onclick = function(e) {
      e.preventDefault();
  }
  ```

- event.stopPropagation 阻止冒泡





### 事件委托

根据冒泡的特性可知，如果给父节点设置了一个点击事件，这时候我们点击子结点，则也会触发该事件，这就是事件委托，可以用来增加效率

例如如果想给ul里面的所有li添加点击事件，则无需给每个li添加事件，而是将事件绑定在ul上，这样也能实现同样的效果，但却只给一个元素添加了一个事件，从而提高了效率





### 常用鼠标事件

- `contextmenu` 控制如何显示上下文菜单，即鼠标右键的菜单，在该事件的处理程序中使用e.preventDefault可以取消右键菜单

- `selectstart` 在该事件的处理程序中使用e.preventDefault可以禁止选中文字

- `mouseEvent` 鼠标事件对象

  - e.clientx/e.clientY 鼠标在可视区的点击坐标
  - e.pageX/e.pageY 鼠标在整个页面内的点击坐标

- `mousemove` 鼠标移动事件

- `mousedown `鼠标按下事件

- `mouseup `鼠标松开事件

- `mouseover`鼠标经过事件，经过子盒子时也会触发

- `mouseenter`鼠标经过事件，经过子盒子时不会触发

- `mouseleave` 与mouseenter搭配的鼠标离开事件

  图片跟随鼠标移动案例

  ```html
  <body>
      <img src="../../images/1.jpg" alt="">
      <script>
          var img = document.querySelector('img');
          document.addEventListener('mousemove', function(e) {
              var x = e.pageX;
              var y = e.pageY;
              img.style.top = y - 50 + 'px';
              img.style.left = x - 50 + 'px';
          })
      </script>
  </body>
  ```







### 常用键盘事件



`onkeyup` 按键弹起，不区分大小写

```html
<body>
    <script>
        document.addEventListener('keyup', function() {
            console.log('up');
        })
    </script>
</body>
```

`onkeydown` 按键按下，不区分大小写

`onkeypress`也代表按键按下，但是不识别功能键，例如ctrl，区分大小写



**键盘事件对象**

- e.key 得到按下的键
- e.keycode 得到按下键的ascii码，利用keycode可以判断用户按下了哪个键









# BOM

BOM译为浏览器对象模型，提供了与浏览器窗口进行交互的对象

DOM中的顶级对象是document，BOM中的顶级对象是window

BOM比DOM的范围更大，即window中包含了document

定义在全局作用域中的变量，函数都会变成window的属性和方法，且window可省略，例如alert其实完整写法是window.alert()



## 页面加载事件

`window.onload`可以使其内的内容在文档内容全部加载完成后再执行，因此使用window.onload就可以将script标签写在任意位置了,例如此处将script标签写在了div的上面 

```html
<body>
    <script>
        window.onload = function() {
            var d = document.querySelector('div');
            d.onclick = function() {
                alert('123');
            }
        }
    </script>
    <div>1234</div>
</body>
```



但是上面这种写法只能有一个window.onload，如果写了多个则以最后一个为准，可以使用addEventListener来写多个onload事件

```html
<script>
    window.addEventListener('load', function() {
        var d = document.querySelector('div');
        d.onclick = function() {
            alert('23');
        }
    })
</script>
```



load是等页面内容全部加载完毕后才执行，有时效率会比较慢，使用`DOMContentLoaded`可以在DOM加载完成后就执行

```html
<body>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            alert('123');
        })
    </script>
    <div>1234</div>
</body>
```





## 调整窗口大小事件

当窗口的大小发生变化时触发事件

window.innerWidth可以获得当前屏幕宽度

```javascript
window.onresize = function() {};
window.addEventListener('resize', function() {});
```





## 定时器

setTimeout(函数，延迟时间); 可以让某一函数延迟指定的时间后再启动,这里的延迟时间单位是毫秒

clearTimeout(setTimeout函数) 可以清楚某个setTimeout函数

`setInterval`与setTimeout基本类似，只不过他的作用是每间隔一定时间就调用函数

`clearInterval`可以清除某个`setInteval`函数





## JS执行机制

js执行时分为同步任务和异步任务，回调函数都是异步任务

同步任务执行完成后才会执行异步任务

所有的异步任务会在触发事件时放入消息队列，消息队列中的任务在同步任务都执行完以后，再依次执行，如果不触发事件，则异步任务就不会放入消息队列，先触发则先进入消息队列，例如

```javascript
console.log(1);//同步任务，先执行
document.onclick = fn();//异步任务，当前不执行，当触发点击事件时进入消息队列
console.log(2);//同步任务，执行
setTimeout(fn, 3000);//异步任务，当前不执行，当延时3秒以后进入消息队列
```

主线程会不断尝试从消息队列中获取异步任务，称为事件循环



## location



### 获取&修改url

location.href 获取或得到当前页面的url



### 获取url参数

```html
<body>
    <form action="a.html">
        <input type="text" name="userName">
        <input type="text" name="userID">
        <input type="submit" value="登录">
    </form>
</body>
```

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220319174343843.png" alt="image-20220319174343843" style="zoom:33%;" />

提交后页面会跳转至form标签的action，即a.html中，同时url中会得到两个input中输入的内容

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220319174407081.png" alt="image-20220319174407081" style="zoom:67%;" />

此时在a.html中使用`location.search`可以得到该页面中的参数

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220319174612674.png" alt="image-20220319174612674" style="zoom: 67%;" />

```html
<script>
    var params = location.search.substr(1);//把？去掉
    var arrs = params.split('&');//将params以&分成俩个数组
    console.log(arrs[1]);//得到userName=origin
</script>
```



### location对象方法

- location.assign() 跳转页面，且记录历史，可以后退
- location.replace() 跳转页面，不记录历史，不能后退
- location.reload() 重新刷新页面





# offset

offset即偏移量，通过offset相关属性可以动态地得到元素地位置，大小等



element.offsetTop/element.offsetLeft 返回元素相对带有定位父元素上方/左侧的偏移量,如果父元素没有定位或者没有父元素，则以body为准

element.offsetHeight/element.offsetWidth 返回元素的宽高，包含padding，margin，border



## 拖动盒子案例

```html
<body>
    <div class="father">
    </div>
    <script>
        var father = document.querySelector('.father');
        father.addEventListener('mousedown', function(e) {//鼠标按下事件
            //获取鼠标在盒子内的坐标，即鼠标距离页面的距离减去盒子距离页面的距离
            var x = e.pageX - father.offsetLeft;
            var y = e.pageY - father.offsetTop;
            document.addEventListener('mousemove', move);//鼠标移动事件
            function move(e) {
                //盒子的移动，即获取盒子距离页面的坐标，即鼠标距离页面的距离减去鼠标距离盒子的距离
                father.style.left = e.pageX - x + 'px';
                father.style.top = e.pageY - y + 'px';
            }
            //鼠标松开事件
            document.addEventListener('mouseup', function() {
                //移除鼠标移动事件
                document.removeEventListener('mousemove', move);
            })
        })
    </script>
</body>
```





# client

element.clientWidth/element.clientHeight 返回自身包括padding的宽度/高度，不含边框 





# 立即执行函数

即无需调用，立即执行的函数,其形式如下

- (function(){})()
- (function(){}())



# scroll

scroll为页面滚动事件

element.scrollWidth/element.scrollHeight 返回自身**实际**的宽度/高度，即实际内容的高度，不含边框

element.scrollTop/element.scrollLeft 返回被卷去的上侧/左侧距离，window.pageYOffset获得页面被卷去的头部





# 封装动画函数

使obj移动到距页面左侧pos距离的位置，这里的obj需要设置为定位

```javascript
//缓动效果
function move(obj, pos, callback) { //callback为回调函数
    clearInterval(obj.timer);
    obj.timer = setInterval(function() {
        var step = (pos - obj.offsetLeft) / 10;
        step = step > 0 ? Math.ceil(step) : Math.floor(step);
        if (obj.offsetLeft == pos) {
            clearInterval(obj.timer);
            if (callback) callback();//当定时器结束时调用回调函数
        }
        obj.style.left = obj.offsetLeft + step + 'px';
    }, 15);
};
```

