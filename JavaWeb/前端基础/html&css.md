# hello,html

## html概念

网站由网页构成，网页即为html文件（超文本标记语言）



## 标签类型

```html
<!--标签通常是成对出现的-->
<html></html><!--双标签-->
<br /><!--单标签-->

<!--标签关系分为包含和并列
html标签包含head和body标签，head和body标签为并列-->
<html>
    <head>
        
    </head>
    <body>

    </body>
</html>
```



## 基本骨架

```html
<!--基本结构标签-->
<html>
    <head>
        <title>hello,html</title><!--title是网页标题-->
    </head>
    <body>
        The first html of origin.
    </body>
</html>

<!--1. <!DOCTYPE html>是文档类型声明标签，不属于html标签，必须写在最开头,表示采用html5版本来显        示网页。
    2. <html lang="en">用来定义当前文档显示的语言，en为english，zh-CN为chines。-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>hello,html!</title>
</head>
<body>
    Write code is a very happy thing!
</body>
</html>
```



# html常用标签

## 标题标签

1. 分为h1-h6六个等级，重要性依次递减

```html
<body>
    <h1>head1</h1>
    <h2>head2</h2>
    <h3>head2</h3>
    <h4>head2</h4>
    <h5>head2</h5>
    <h6>head2</h6>
</body
```



## 段落标签

1. html中的大段空格在网页中最终只会显示一个空格。
2. 段落标签用于形成段落。

```html
<body>
    <p>this is the paragraph1.</p>
    <p>this is the paragraph2.</p>
    <p>this is the paragraph3.</p>
</body>
```



## 换行标签

换行标签是一个单标签

```html
<body>
    hello, html<br />hello, html
</body>
```



## 文本格式化标签

### 加粗

```html
<body>
    <strong>加粗的文字</strong> <b>加粗的文字</b>
</body>
```



### 倾斜

```html
<body>
    <em>倾斜的文字</em> <i>倾斜的文字</i>
</body>
```



### 删除线

```html
<body>
    <del>删除线文字</del> <s>删除线文字</s>
</body>
```



### 下划线

```html
<body>
    <ins>下划线文字</ins> <u>下划线文字</u>
</body>
```



## div和span标签

1. div是布局标签，每个div标签独占一行
2. span是布局标签，一行中可以有多个span标签

```html
<body>
    <div>this is a div tag</div>
    <span>this is a span tag</span>
</body>
```



## 图像标签

### alt

当图片未能成功加载时显示alt

```html
<body>
    <img src="image.jpg" alt="图像未能正确显示" />
</body>
```



### titie

当把光标放在图片上时显示title

```html
<body>
    <img src="image.jpg" title="图片注释" />
</body>
```



### width&weight

width和weight控制宽和高，当只调节宽或高时，图片尺寸会等比例调节

```html
<body>
    <img src="image.jpg" width="800" height="400" />
</body>
```



### border

border调节黑色边框尺寸

```html
<body>
    <img src="image.jpg" border="15" />
</body>
```



## 链接标签

1. href中可以放外部链接，内部链接，下载链接（zip/exe......）
2. href中放入#表示空链接
3. target表示打开方式，默认为_self，表示当前窗口打开， _blank表示在新窗口中打开

```html
<body>
    <a href="http://www.qq.com" target="_self">腾讯1</a>
    <a href="#" target="_blank">腾讯2</a>
</body>
```

### 锚点链接

1. href中加上#表示一个锚点链接，锚点链接可以跳转到当前页面的某一标题处
2. 锚点链接中#后的内容表示的是要跳转到的标题的id

```html
<body>
    <h1 id="content1">content1</h1>
    <a href="#content1">jump to content</a>
</body>
```



## 表格标签

1. table标签表示表格，tr标签即table row，表示表格中的每一行，td标签即table data，表示表格中每一行的每个单元格。
2. th即table head，为表头单元格。
3. thead和tbody为表格的结构标签。

```html
<body>
    <table>
        <thead>
            <tr><th>姓名</th><th>性别</th><th>民族</th></tr>
        </thead>
        <tbody>
            <tr><td>origin</td><td>男</td><td>汉</td></tr>
        	<tr><td>jack</td><td>男</td><td>汉</td></tr>
        </tbody>
    </table>
</body>
```





### 合并单元格 

1. rowspan为跨行合并，目标单元格为要合并的单元格之中最上方的单元格
2. colspan为跨列合并，目标单元格为要合并的单元格之中最左侧的单元格
3. 标签中的数字表示要合并的单元格的个数
4. 注意合并单元格时需要删去多余的单元格

```html
<body>
    <table border="1" width="500" height="249" cellspacing="0">
        <tr><th>head1</th>  <th>head2</th>  <th>head3</th></tr>
        <tr><th rowspan="2"></th>  <th colspan="2"></th> </tr>
        <tr> <th></th>  <th></th></tr>
    </table>
</body>
```



## 列表标签

### 无序列表

ul标签里面只能放li标签

```html
<body>
    <ul>
        <li>op1</li>
        <li>op2</li>
        <li>op3</li>
        <li>op4</li>
    </ul>
</body>
```





### 有序列表

ol标签里面只能放li标签

```html
<body>
    <ol>
        <li>op1</li>
        <li>op2</li>
        <li>op3</li>
        <li>op4</li>
    </ol>
</body>
```





### 自定义列表

1. dl表示自定义列表，dt意为列表主题，dd为对dt分支或解释说明。
2. dl标签中只能由dt和dd标签。
3. 一个dl标签中可以有多组dt和dd标签

```html
<body>
    <dl>
        <dt>op1</dd>
        <dd>op2</dd>
        <dd>op3</dd>
        <dd>op4</dd>
    </dl>
</body>
```



##  表单标签

1. 表单域是一个包含表单元素的区域，用form标签来表示一个表单域。
2. 表单控件（表单元素）分为
   - input输入表单元素
   - select下拉表单元素
   - textarea文本域元素





### 表单域

即form标签

- action 表示该表单提交后转到的url地址
- method 表示提交方式
- name 表示表单域名称



### input表单元素

1. 使name值相同可以实现多选一。
1. checked="checked“意为设置默认选项。

```html
<body>
    <form>
        <!-- text是正常的文本框 -->
        userID<input type="text">
        <!-- password是隐藏显示的密码框 -->
        password<input type="password">
        <!-- radio是单选按钮 -->
        sex：man<input type="radio" name="sex" checked="checked"> woman<input type="radio" name="sex">
        <!-- checkbox是多选按钮 -->
        hobby：man<input type="checkbox"> woman<input type="checkbox">
        提交按钮<input type="submit">
        <!--button是普通按钮-->
        <button>按钮</button>
    </form>
</body>
```





### label标签

label标签可以使某个元素和input标签绑定。

绑定的依据为input的id和label的for

```html
<body>
    <label for="id">option1</label><input type="radio" id="id">
</body>
```





### select表单元素

1. select为下拉表单元素
2. selected="selected"意为设置默认选项

```html
<body>
    <form>
        <select>
            <option>one</option>
            <option>two</option>
            <option>three</option>
            <option selected="selected">four</option>
        </select>
    </form>
</body>
```





### textarea

1. textarea是文本域表单标签

```html
<body>
    <form>
        <textarea>
            这里是默认显示文字
        </textarea>
    </form>
</body>
```





# hello,css

1. css是层叠样式表的简称。
2. css同html，也是一门标记语言。

3. css规则由两个主要的部分组成：选择器以及一条或多条声明。选择器指定对象，声明语句指定具体样式，
4. css语句一般写在head标签中的style标签中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>hello,html!</title>
    <style>
        p {
            color: red;
            font-size: 15px;
        }
    </style>
</head>
<body>
    <p>hello,css</p>
</body>
</html>
```



# CSS选择器

1. 选择器，就是选择某个标签。
2. 选择器分为基础选择题和复合选择器
3. 基础选择器是由单个选择器组成的.





## 基础选择器



### 标签选择器

以标签名作为选择器，修改指定类型标签样式。

```html
<style>
        p {//修改所有的p标签
            color : green;
        }
</style>
```



### 类选择器

声明一个color类选择器

```html
<style>
        .color {
            color : red;
        }
</style>
```

在需要类选择器的标签中调用该选择器，一个标签可以同时调用多个类选择器，中间用空格隔开就好

```html
<body>
    <p class="color">test</p>
    <div class="color">test</div>
</body>
```



### id选择器

声明一个id选择器

```html
<style>
        #pink {
            color: pink;
        }
    </style>
```

在需要id选择器的标签中调用该选择器，且区别于类选择器，id选择器只能调用一次。

```html
<body>
    <p id="pink">test</p>
</body>
```



### 通配符选择器

通配符选择器修改所有的标签。

 ```html
 <style>
     * {
         color : red;
     }
 </style>
 ```



## 复合选择器

复合选择器是对基础选择器进行组合形成的。





### 后代选择器

表示选出ol里面的所有li标签

```html
<style>
	ol li {
	color: pink;
	}
</style>
```





### 子元素选择器

表示选出aclass类中的div标签的**直接**子标签

```html
<style>
    .aclass div>p {
        color: pink;
    }
</style>
```





### 并集选择器

表示选出div标签和p标签。

```html
<style>
    div,
    p {
        color: pink;
    }
</style>
```





### 伪类选择器

#### 链接伪类选择器

假设有一个链接标签

```html
<body>
    <a href="http://www.bilibili.com">进入b站玩耍吧</a>
</body>
```

添加链接伪类选择器

注意添加顺序是固定的，hover在active之前。

```html
<style>
    a:hover {//当鼠标指针位于其上时
        color: pink;
    }
    a:active {//当鼠标点击未弹起时
        color: red;
    }
</style>
```





#### :focus伪类选择器

主要针对于表单元素

```html
<style>
    input:focus {//当光标定位于表单上时
        color: pink;
	}
</style>
```







# CSS字体属性

```html
width
weight
font-size : 14px;
background-color
fond-family//设置字体
font-weight : 700;//设置粗体文字
font-style
font : font-style font-weight font-size/line-height font-family//复合字体属性,顺序不能变
```



# CSS文本属性

```html
color
text-align : center/left/right //文本水平对齐方式
text-decoration : none/underline/overline/lin-through; //装饰文本
text-indent : 20px/2em;//首航缩进, em为字体大小的相对单位
 line-height : 26px;//行间距
```

# CSS样式表

## 内部样式表

是指将css写到html页面内部，单独放到一个style标签中。



## 行内样式表

直接在标签中设置style标签，控制当前标签。

```html
<body>
    <p style="color: pink;">test</p>
</body>
```



## 外部样式表

先新建一个CSS文件，写入CSS代码

```html
p {
    color: pink;
}
```

在html文件中的head中使用link标签引入指定的css文件

```html
<link rel="stylesheet" href="test.css">
```





# CSS元素显示模式

元素显示模式就是元素以什么方式显示。

html元素一般分为**块元素**和**行内元素**两种类型。

1. 块元素独占一行，行内元素一行可以显示多个。
2. 块元素可以修改高和宽；行内元素不能修改高和宽，其默认宽度就是其内容的宽度

**行内块元素**同时具有行内元素和块元素的特点，一行可以显示多个，且可以设置宽度和高度。



## 元素显示模式的转换

```html
<style>
    a {
        display: block;//将行内元素转换为块元素
    }
    div {
        display: inline;//将块元素转换为行内元素
    }
    span {
        display: inline-block;//将行内元素转换为行内块元素
    }
</style>
```





# CSS背景

```html
backgroud-color: pink;//背景颜色，默认为transparent
background-image: url(image.png);//背景图片，默认为none
background-repeat: repeat/no-repeat/repeat-x/repeat-y//平铺
background-position: 水平方向 垂直方向/x坐标，y坐标
background-attachment: scroll/fixed //背景图像固定
```



# CSS三大特性

## 层叠性

样式重叠时遵循就近原则。



## 继承性

子标签会继承父标签的某些样式。



## 优先级

当同一个元素指定多个选择器时，如果选择器相同，则执行重叠性；

当选择器不相同时：

1. 行内样式表>id选择器>类选择器>元素选择器

2. 权重最大的为!important

```css
color: pink!important;
```

3. 继承的权重为0
4. 在复合选择器中，权重是可以叠加的





# 盒子模型

盒子模型的组成：border边框，content内容，padding内边距，margin外边距



## 边框

边框会影响盒子的实际大小

边框三个属性，可以组合成复合写法

```css
border-width//边框粗细、
border-style: solid;//边框类型
border-color//边框颜色
border-top-left-radius//使边框的角有弧度
```



指定边框的某一条边,并采用复合写法

```css
border-top: 2px bolid pink;
border-bottom
border-left
border-right
```



合并相邻的边框

```css
border-collapse: collapse;
```



## 内边距

内边距会影响盒子的实际大小，但是如果盒子本身并没有指定width或heigth属性，此时padding不会撑开盒子的大小。

```css
padding-top
padding-bottom
padding-left
padding-right
```



使用复合写法时padding:后面跟

1. 四个值，分别代表 上，右，下，左
2. 两个值，分别代表 上下，左右
3. 三个值，分别代表 上，左右，下
4. 一个值，代表上下左右





## 外边距

外边距用于控制盒子和盒子之间的距离。

```css
margin-top
margin-right
margin-buttom
margin-left
```



margin的复合写法规则与padding相同。



## 盒子阴影

```html
box-shadow:水平阴影位置 垂直阴影位置 阴影模糊度 阴影大小 
```





# 浮动



## 浮动特性

1. 浮动元素会脱离标准流，当一个盒子被设置为浮动后，其原有位置会空出，并且可能会被其他未浮动的盒子占用，从而出现重叠现象。
2. 如果多个盒子都设置了浮动，则会按照属性值一行内显示并按顶端对齐。
2. 浮动的盒子只会影响其后面盒子的标准流，不会影响前面的标准流。



## 清除浮动

由于父级盒子很多情况下，不方便给高度，此时如果将子盒子设为浮动，则会导致父盒子高度为0，影响下面的布局。

清除浮动的本质是清除浮动元素脱离标准流所造成的影响。



### 额外标签法

声明clear类

```css
.clear {
    clear: both;
}
```



在需要清除浮动的子盒子后加上一个额外的clear标签，注意该标签必须是块级元素。

```html
<body>
<div class="box">
    <div class="child"></div>
    <div class="child"></div>
    <div class="child"></div>
    <div class="clear"></div>
</div>
<div class="bottom"></div>
</body>
```



### 父级添加overflow

给父级盒子添加以下代码以清除浮动

```css
overflow: hidden;
```



### :after伪元素法

```css
.clearfix:after {
	content: "";
	display: block;
	hight: 0;
	clear: both;
	visibility: hidden;
}

.clearfix {
	*zoom: 1;
}
```



### 双伪元素清除浮动

```css
.clearfix:before,
.clearfix:after {
    content: "";
    display: table;
}

.clearfix:after {
    clear: both;
}

.clearfix {
    *zoom: 1;
}
```







# 定位

定位可以让盒子自由地在某个盒子内部移动位置或者固定，并且可以压住其他盒子。





## 定位组成

 定位 = 定位模式 + 边偏移



### 定位模式

定位模式决定元素的定位方式，通过css的positon属性设置



#### 静态定位static

是元素的默认定位方式，即无定位，与标准流相同



#### 相对定位relative

1. 相对定位在移动位置时，是以其原来的位置作为参照点移动的
2. 移动后，原来的位置继续占有，不脱离标准流，即不影响后面的盒子

```css
.box {
    position: relative;
    top: 50px;//与原来位置的顶部相距50px
    left: 100px;
}
```



#### 绝对定位absolute

1. 绝对定位在移动时，是以其祖先元素为参照点移动的
2. 如果没有父级盒子，那么则以浏览器边框为参照点定位
3. 如果有父级盒子，但是所有父级盒子都没有设置为定位，那么仍然以浏览器边框为参照点定位
4. 如果有父级盒子，并且父级盒子设置了定位，那么则以最近的一级设置了定位的父盒子为参照点移动。例如父亲没有定位但是爷爷有定位，那么就以爷爷为参照点。、
5. 绝对定位移动后不再占有原先位置，即脱离标准流



#### 固定定位fixed

1. 固定定位可以使元素固定在浏览器可视区的某个位置，即在页面滚动时会跟随页面移动
2. 以浏览器的可视窗口为参照位置
3. 不占有位置，脱离标准流



#### 粘性定位

```css
position: sticky; top: 10px;/*表示当元素距离浏览器顶部10px以内时，就固定在可视区，距离浏览器顶部10px以外时就不再固定，例如bilibili首页顶部的导航栏*/
```

1. 以浏览器的可视区为参照点
2. 占有原先位置，不脱离标准流
3. 必须添加top，bottom，left，right其中一个才有效

### 边偏移

边偏移用于确定定位元素相对于参照物的位置

有top，bottom，left， right四个属性，代表与参照物的相距位置

```css
.box {
    position: relative;
    top: 50px;//与参照物顶部相距50px
    left: 100px;
}
```



## 子绝父相

指的是孩子使用绝对定位，父亲使用相对定位

1. 孩子使用绝对定位，以便能定位在父盒子的指定位置，同时不占有空间，可以覆盖在父元素上方
2. 如果孩子使用绝对定位那么父亲也必须要有定位，父亲使用相对定位比较合适，因为相对定位不脱离标准流，不会影响其他盒子





## 定位叠放次序

使用定位布局时，可能会出现盒子重叠的情况，使用z-index来确定盒子的前后次序

z-index数值可以是正整数，负整数，0，默认是auto，数值越大，盒子越靠近外层

如果没有设置数值，则按照书写顺序后来者居上

```css
z-index: 1;
```



## 定位特殊特性

1. 行内元素添加绝对或者固定定位，可以直接设置高度和宽度
2. 块级元素添加绝对或固定定位，如果不给宽度和高度，默认大小是内容的大小





# 元素的显示与隐藏



## display

```css
display: none;/*隐藏对象,且隐藏后不再占有原位置*/
display: block;/*除了转换为块级元素还有显示元素的意思*/
```



## visibility

```css
visibility: hidden;/*元素隐藏，但仍占有原位置*/
visibility: visible;/*元素可视*/
```



## overflow

```css
overflow: hidden;/*将多出的部分隐藏*/
overflow: scroll;/*添加滚动条*/
overflow: auto;/*益处使才显示滚动条*/
```



# css常用技巧



## 三角的做法

当盒子只有边框时，它的四个边框其实是四个三角形，如下图就是一个<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220305200144395.png" alt="image-20220305200144395" style="zoom:25%;" />

```css
.box {
    height: 0px;
    width: 0px;
    border: 30px solid transparent;
    /*border-top-color: pink;*/
    border-left-color: green;
    border-right-color: skyblue;
    border-bottom-color: red;
}
```



## 取消表单轮廓

表单元素在光标定位时会有蓝色轮廓，不好看，使用下面代码取消该轮廓

```css
input {
    outline: none;
}
```



## 取消拖拽文本框

取消文本框下面的那个拖拽按钮

```css
textarea {
    resize: none;
}
```



## 设置行内块和文本的对齐方式

```css
vertical: baseline | top | middle | bottom;
```



## 溢出的文字显示省略号

先强制一行显示文本

```css
white-space: nowrap;(默认normal自动换行)
```



再将溢出的部分隐藏

```css
overflow: hidden;
```



添加省略号

```css
text-overflow: ellipsis;
```







# css3新特性



## 新增选择器



### 属性选择器



选择标签为input并且有value属性的元素

```css
input[value] {
    color: pink;
}
```



选择标签为input并且value值为001的元素

```css
input[value="001"] {
    color: pink;
}
```



 选择标签为div，并且类名以icon开头/结尾/存在的元素

```css
div[class^=icon] {
    color: pink;
}
div[class$=icon] {
    color: pink;
}
div[class*=icon] {
    color: pink;
}
```



### 结构伪类选择器

选择ul下的指定li标签元素

```css
ul li:first-child {//ul下的第一个li
    background-color: pink;
}

ul li:last-child {//ul下的最后一个li
    background-color: pink;
}

ul li:nth-child(4) {//ul下的第4个li
    background-color: pink;
}
```

nth-child()括号中的参数可以是

1. 数字，表示第几个元素、

2. even，表示偶数，odd，表示奇数

3. 与n有关的表达式，例如

   ```css
   ol li:nth-child(n) {
       color: pink;
   }
   ```

   表示n从0开始，每次执行ol li:nth-child(n)选择器，然后加一再执行，一直到所选择的元素没有第n个child为止

   **注意**

   - 这里的字母必须是n不能是其他字母
   - n为0时默认不执行
   - 这里的n可以是与n有关的表达式，例如2n
   - 如果括号里选中的元素不是前面指定的li，那么就不会执行



nth-child会给选定元素的每一个孩子按顺序编号，下面的nth-tof-type则可以给指定类型的孩子编号，例如

```css
div li:nth-of-type(1) {
    color: pink;
}
```

表示将div中的li编号，并选择其中编号为1的元素





### 伪元素选择器

```css
div::before {//在div的内部的最前面创建一个盒子，盒子的内容写在content中
    content:'before';
}
div::after {//在div的内部的最后面创建一个盒子，盒子的内容写在content中
    content:'after';
}
```

伪元素创建的盒子是行内元素







## CSS3盒子模型

默认的盒子模型的大小为padding + border + width&height，使用以下语句可设置盒子模型大小为width&height的大小

```css
box-sizing: border-box;
```



## calc

calc函数里可以放表达式

```css
width: calc(100% - 30px);/*比父盒子小30px*/
```



## 过渡

表达式：transition: 属性 花费时间 运动曲线 何时开始

属性：指想要产生过度效果的属性

花费时间：单位是秒（s），单位必须要写

运动曲线：默认是ease，一般采用默认即可

何时开始：默认是0s，单位必须要写

```css
.box {
    width: 100px;
    height: 100px;
    background-color: pink;
    transition: width 0.5s, height 0.5;//使box的width和height在变化时产生过度效果, 直接写all可以表示所有的属性
}
.box:hover {//用hover产生有过度效果的变化
    width: 150px;
    height: 150px;
}
```



## 2D转换

转换关键词为`transform`



### 位移

移动x坐标，y坐标

`translate`不会影响其他的盒子（参考b站导航栏右侧菜单的浮动效果）

`translate`对于行内元素是无效的

```css
transform: translate(100px, 100px);//x坐标，y坐标
transform: translateX(100px);//x轴方向移动100px
transform: translateY(50%);//y轴方向移动自身大小的50%
```



### 旋转

rotate的单位是deg，代表度数

角度为正代表顺时针，为负代表逆时针

默认的旋转中心是元素的中心点

```css
transform: rotate(45deg);
```



设置旋转的中心点

```css
transform-origin: left bottom;//左下角
transform-origin: 50px 50px;
```



### 缩放

scale缩放不会影响其他位置，而且可以设置缩放中心点

```css
transform: scale(1.5, 1.5);//宽度和高度变为原来的1.5倍
transform: scale(1.5);//等比例缩放
```



### transform的综合写法

因为是按顺序执行的，所以一般把位移放在第一位

```css
transform: translate(100px, 0) rotate(180deg) scale(2);
```



## 动画

先创建动画

0%代表动画开始时的状态，100%代表动画结束时的状态,这两个数也可以改成 `from` 和 `to`

不仅仅可以写初始状态和结束状态，还可以写某个中间状态，例如50%，75%等等，这些百分比代表的就是时间的划分

```css
@keyframes move {
    0% {
        transform: translate(0px);
    }
    100% {
        transform: translate(500px, 0);
    }
}
```



再调用动画，指定动画名和动画持续时间

```css
animation-name: move;
animation-duration: 1s;
```



### 动画常用属性

```css
animation-delay: 1s;//规定动画何时开始
animation-iteration-count: infinite;//设置动画重复次数，infinite代表一直重复，也可以写次数
animation-direction: alternate;//设置反向播放
animation-fill-mode: forwards;//动画结束后停在该位置
animation-play-state: paused;//设置动画的启动和暂停
```



animation-timing-fuction规定动画的速度曲线，默认是`ease`

```css
linear:匀速
ease：默认
ease-in：以低速开始
ease-out：以低速结束
ease-in-out：以低速开始和结束
steps()：设置步长
```



## 3D转换



 ### 3D位移

z轴上正值代表向外移动				

```css
transform: translateX(100px) translateY(100px) translateZ(100px);
transform: translate3D(100px, 100px, 100px);
```



### 透视

想要给元素实现3D效果，则需要给父盒子加透视

 ```css
 perspective: 300px;
 ```





### 3D旋转

```css
transform: rotateX(100deg);//沿着x轴旋转100deg
transform: rotate3d(100px, 100px, 100px, 45deg);//沿着自定义轴旋转deg角度
```



默认情况下子元素不开启3D立体环境

```css
transform-style: flat;//默认，子元素不开启3D
transform-style: preserve-3D;//子元素开启3D
```



# 项目帮助



## 网页标签上的favicon

得到ico文件存放在根目录下，然后将下列语句放在head标签下即可

```css
<link rel="shortcut icon" href="logo.ico" />
```



## 常用类名

shortcut-快捷导航栏



## 添加字体图标

1. 先下载字体图标文件<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220308002354248.png" alt="image-20220308002354248" style="zoom: 25%;" />

2. 将fonts文件复制到根目录下

3. 在css文件中引入字体图标

4. 在需要使用字体图标的位置写上对应数字和声明family

   ```css
   font-family: 'icomoon';
   ```





## LOGO SEO优化

1. logo盒子里面先放一个h1标签，目的是为了告诉搜索引擎该位置权重很大
2. h1里面再放一个链接，该链接可以返回首页，将该链接的背景设置为logo图片即可
3. 链接里要放说明文字，但是改文字不显示，即设置font-size = 0
4. 给链接一个title属性，这样鼠标放到logo上就能看到提示文字





## 使用一个元素的hover控制另一个元素

例如有以下样式

<img src="C:\Users\wild hearts\AppData\Roaming\Typora\typora-user-images\image-20220308170553783.png" alt="image-20220308170553783" style="zoom: 33%;" />

```html
<div id="search" class="search fl">
    <input id="input" type="search" name="" id="" placeholder="小破站">
    <button id="button"></button>
</div>
```

现在想要实现当鼠标悬停在search上时，search，input和button都变色

则可用id来实现

```css
.search:hover {
    background-color: white;
}

#search:hover #input {
    background-color: white;
}

#search:hover #button {
    background-color: white;
}
```

```css
/* 情景一：两个是兄弟元素 */
.box:hover+.change {
    color: red;
}
/* 情景二：两个是父子元素 */
.box:hover .change {
    color: red;
}
/* 情景二：两个是兄弟元素,改变的是一个兄弟元素的子元素 */
.box:hover+#c>.change {
    color: red;
}
/* 情景四：设置多个同级元素的样式 */
.block:hover {
    background-color: blue;
}
```







# 响应式布局



 ## em



有如下标签

```html
<body>
    <div class="box">
        <p></p>
    </div>
</body>
```



在样式中，父盒子的文字大小是12px，那么对于子盒子p来说，一个em单位就是12px，因此这里的p的width和height实际上都是120px

```css
.box {
    font-size: 12px;
}

.box p {
    width: 10em;
    height: 10em;
    background-color: pink;
}
```



## rem

`rem`也是一种字体大小，它是相对于`html`这个标签来说的，例如这里的p的实际大小是150px * 150px

```css
html {
    font-size: 15px;
}

p {
    width: 10rem;
    height: 10rem;
    background-color: pink;
}
```





## 媒体查询

使用`@media`查询，可以针对不同的屏幕尺寸设置不同的样式



### 查询类型

- all 所有设备
- print 打印设备
- screen 电脑手机屏幕



### 关键字

- and 
- not
- only



### 媒体特性

- width
- min-width
- max-width



### 语法

表示在屏幕上，并且当最大宽度小于等于800px的时候，将body标签的背景色改为pink

```css
@media screen and (max-width: 800px) {
    body {
        background-color: pink;
    }
}
```







# less

less是css的一门扩展语言，它包含了css，并且在css的基础上增加了一些特性。

less需要转为css文件才能在html中引入。

- less增加了变量这一特性，less中的变量一定要加@

  ```less
  @color: pink;//这里的@color就是一个变量
  body {
      background-color: @color;
  }
  ```

  



- less中可以直接在父标签里面写子标签选择器,如果是伪类选择器的话就要在前面加个&

  ```less
  .box {
      p {
          background-color: skyblue;
      }
      &:hover {
          background-color: green;
      }
  }
  ```



- less中可以使用运算符,当参与运算的两个数的单位不同时，以第一个数的单位为准





# css变量

定义css变量

- 选择器表明的变量的作用域
- 变量名使用--开头

```css
html {
    --mian-color: red;
}
```



使用css变量

```css
.box {
    color: var(--main-color);
}
```

