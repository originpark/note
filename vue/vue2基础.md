# hello,vue

下载vue.js,并在html中引入

```html
<script src="../js/vue.js"></script>
```



在js代码中创建Vue对象,使用Vue对象实例给某个盒子内注入值

```html
<script type="text/javascript">
    Vue.config.productionTip = false; //设置为false以阻止vue在启动时生成生产提示
    //new 一个Vue对象实例
    const c = new Vue({
        el: '#box1', //el用于指定当前对象实例为哪个容器服务,值通常为css选择器字符串
        data: { //data用于保存数据,数据只提供给el指定的容器使用,其值可以是一个对象
            name: 'origin',
            age: 20
        }
    })
</script>
```



在html中,使用`{{}}`来表示一个插值符,占位符中的值由某个vue对象实例维护

```html
<!-- {{}}为Vue中的插值符 -->
<h1 id="box1">hi, this is {{name}}.{{age}}.com</h1>
```



**el和data的第二种表示方法**

```javascript
Vue.config.productionTip = false;
const x =  new Vue({
    data:function () {
        return {
            value: 'auto flush',
        }
    }
})
x.$mount('.box1');
```







**注意点**

1. 容器和vue对象实例之间是一一对应的关系,即不能用一个vue对象去管理多个容器, 也不能用多个vue对象管理一个容器
2. `{{}}`插值符中不仅可以写Vue对象data属性中定义的数据,也可以写js表达式



# 模板语法

每当vue对象实例中的data数据变化的时候,模板就会被解析一次



## 插值语法

使用插值符`{{}}`,内部填入js表达式或对应的Vue对象中设置的属性名



## 指令语法

### v-bind:

对于标签中的属性来说,其值不能用`{{}}`来插入,而是要使用`v-bind:`前缀

```html
<a v-bind:href="url">goto bilibili</a>
```

加上v-bind前缀后,vue会将属性值url看成一个js表达式

`v-bind:`可以简写为`:`

**单向绑定**

`v-bind:`属于单向绑定,即改变data中的属性会影响容器中的内容,但是如果通过input标签改变容器中的内容时,data中的属性不会被改变,概括为数据只能从data流向页面









### v-model:

**双向绑定**

v-model作用大致同v-bind相同,区别在于v-model属于双向绑定,即改变data,容器中对应的内容也会改变,改变容器中的内容的值,data中的数据的值也会改变,概括为数据既能从data流向页面,也能从页面流向data

因此,由于双向绑定,`v-model:`只能用于输入类标签的属性





# mvvm

mvvm是一种模型,vue遵循了这个模型,m指model,即数据层,对应vue对象实例中的data属性,v指view,即视图层,

对应vue所指向的dom容器,vm指viewModel,对应vue对象实例

因此:

1. 我们在创建vue对象实例的时候,通常用vm这个名称来接收vue对象实例
2. data属性中的model数据最终会绑定到vm中(vue对象实例),view(容器)获取的数据实际上是vm中有的数据(vue对象实例以及vue原型对象)





# vue数据代理



**Object.definProperty()方法**

Object.definProperty()方法用于给某个对象添加一个不可被遍历,修改,删除的属性,且该属性的读和写要通过我们自定义的get和set方法来进行,常用于数据代理,例如

```javascript
// 被代理的对象
let person = {
    name: 'origin',
    age: 20,
}
// 代理对象
let proxy = {}
//给代理对象添加一个name属性,该属性用于代理person对象的name属性
Object.defineProperty(proxy, 'name', {
    //当读取proxy的name时,调用get方法,读取到的值为get方法的返回值
    get: function () {
        return person.name
    },
    //当修改proxy的name时,调用set方法
    set: function (value) {
        person.name = value;
    }
})
//给代理对象添加一个age属性,该属性用于代理person对象的age属性
Object.defineProperty(proxy, 'age', {
    get: function () {
        return person.age
    },
    set: function (value) {
        person.age = value
    }
})
```

从上例可以看出,proxy对象的name和age属性能够控制person对象的name和age属性,相当于proxy对象代理了person对象



在vue中,我们使用Vue的构造函数传入了一个data对象,该data对象会被赋值给Vue对象实例的一个叫做`_data`的属性(实际上这里的`_data`并不完全等同于data,因为`_data`中的数据是要与view中的对应数据链接的),然后vue通过`Object.definProperty()`方法,给vue对象实例创建了`_data`对象中的属性的代理对象,因此,可以通过这些代理对象来控制`_data`对象中的属性,`_data`对象中的属性又与模板中的对应数据进行了绑定





# 事件处理



## v-on: & methods

使用`v-on:`指令语法来给标签声明一个事件函数,`v-on:`也可以用`@`代替

```html
<button id="root" v-on:click="getInfo">getInfo</button>
```

这里的函数名getInfo如果不加括号,表示不传参数,但还是会默认传入一个`event`对象

如果加了括号,表示传参,此时不会默认传入`event`对象,如果想要使用`event`对象,就需要使用`$event`的方式手动传入`event`对象

```html
<button id="root" v-on:click="getInfo($event)">getInfo</button>
```



对于事件方法, 用vue对象实例中的`methods`属性来维护

```javascript
new Vue({
    el: '#root',
    methods: {
        getInfo: function (event) {
            alert(event);
        }
    }
})
```

`methods`属性中的方法对象不会被代理,不同于`data`中的对象



## 事件修饰符



### prevent

阻止元素默认事件

```html
<a id="root1" href="https://www.bilibili.com" @click.prevent="show">bilibili</a>
```



### stop

阻止冒泡

```html
<a id="root1" href="https://www.bilibili.com" @click.stop="show">bilibili</a>
```



### once

事件只触发一次

````html
<a id="root1" href="https://www.bilibili.com" @click.once="show">bilibili</a>
````



### passive

默认情况下,如果一个标签绑定了事件函数,那么在触发事件后,会先执行事件函数,事件函数指向完之后,才会触发标签的默认事件,使用passive可以取消这一行为,即不必等到事件函数完成,就能触发标签的默认事件





事件修饰符支持链式编程,例如

```javascript
@click.prevent.stop//先阻止默认事件,再阻止冒泡
```







## 键盘事件

@keyup表示按键按下事件,enter为回车的别名,这里表示当回车被按下时触发show事件

```html
<input type="text" id="root1" @keyup.enter="show">
```

常用的别名: 

`delete`(backspace和delete)

`esc` , `space`, `tab`, `up`, `down`, `leff`, `right`, `enter`

实际上每个按键都有一个自己的key,其值就是该按键的名字,我们也可以直接通过key来绑定事件,例如

```html
<input type="text" id="root1" @keyup.y="show">
```



# 计算属性

计算属性也是vue中的一个属性,名为computed

```javaScript
new Vue({
    el: '#root',
    data: {
        firstName: '张',
        lastName: '三',
    },
    computed: {//computed为计算属性
        fullName: {//计算属性中的每个属性也是被代理属性,通过get和set方法进行读写
            get: function () {
                //这里的this是vue对象实例
                return this.firstName + " " + this.lastName
            },
        }
    }
})
```

vue对计算属性做了缓存优化,因此,计算属性的get方法被调用的时机如下:

1. 第一次读取计算属性时
2. 当计算属性所依赖的属性值发生变化时

其他时候,读取计算属性时,不会调用get方法,而是直接读取缓存中的数据,只不过当计算属性所依赖的属性值发生变化时,才会调用get方法去更新该计算属性



当我们确定某个计算属性只读不写,即只需要get方法时,就可以写成以下简写形式

```JavaScript
computed: {
    fullName: function () {
        return this.firstName + " " + this.lastName
    }
}
```



# 监视

监视为vue对象实例中的名为watch的属性,其作用是监视某个属性,当该属性发生变化时,调用我们自定义的hadler方法

```javascript
watch: {
    word: {//这里的word可以是data中的数据,也可以是computed中的数据
        //immediate: true,//immediate表示是否立即执行handler函数
        //当word的值发生变化时,调用handler函数
        handler: function (newValue, oldValue) {//默认按顺序传入变化后的值和变化前的值
            console.log('天气从' + oldValue + '变成了' + newValue)
        }
    }
}
```



监视的另一种写法,是在vue对象实例创建完成之后

```javascript
vm.$watch('word', {
    handler: function (newValue, oldValue) {
        console.log('天气从' + oldValue + '变成了' + newValue)
    }
})
```



监视的简写形式:当我们只需要实现一个handler函数时,就可以简写监视为

```javascript
watch: {
    property: function(newValue, oldValue) {
    }
}
```







**深度监视**

例如有以下场景,map是一个有多个k-v键值对的对象,我们希望通过监视来监视其中的内部键值对是否发生改变

```javascript
const vm = new Vue({
    el: '#root',
    data: {
        map: {
            key1: 'value1',
            key2: 'value2',
        },
    },
    watch: {
        map: {
            handler: function () {
                console.log('map change......')
            }
        }
    }
})
```

测试发现,该监听无法监听到key1或key2的改变,因为其监听对象map是一个对象,对于watch来说,只有当该对象的地址发生变化时才说明该对象发生了变化,其内的属性发生变化并不能说明该对象发生了变化

如果我们想让watch实现深度监听,即监听对象内的属性,可以声明`deep: true`

```javascript
watch: {
    map: {
        deep: true,
        handler: function () {
            console.log('map change......')
        }
    }
}
```



# 绑定css

使用:(v-bind)来绑定css,且`clsss`属性和`:class`不会冲突且可以结合,`class`中填入固定的基础样式,`:class`中填入可能会变化的动态样式

```html
<div id="root" class="basic" :class="color" @click="changeColor">hello, origin</div>
<script type="text/javascript">
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            colors: ['blue', 'pink', 'orange'],
            cur: 1,
        },
        computed: {
            color: function () {
                return this.colors[this.cur]
            }
        },
        methods: {
            changeColor: function () {
                this.cur = ((++this.cur) % 4)
            }
        }
    })
</script>
```



`:class`中可以接收类名字符串,类名数组,还可以接收一个对象

```javascript
colorObj: {
    blue: true,
    pink: false,
    orange: false
}
```

属性值为true表示将该属性名字符串加入`:class`中,false表示不加入





# 条件渲染

## v-show

v-show属性中填入一个返回值为boolean的表达式,用于控制该元素的显示与隐藏,底层使用的是`display:none`

```html
<div id="root" v-show="1===2">hello,world</div>
```



## v-if&v-else-if&v-else

v-if作用类似于v-show,只是其底层在隐藏元素时会将元素直接删除

```html
<div v-if="n===1">n=1</div>此处成立则else if和else都隐藏
<div v-else-if="n===2">n=2</div>
<div v-else="n===3">n=3</div>
```

注意if,else if,else这几个元素之间不能被打断, 要连在一起才能使用

这三个标签可以配合`template`标签使用,这是一个无意义的标识标签,在最终解析时会被删去

```html
<template v-if="n===1">
	//......
</template>
```



# 列表渲染



## v-for

v-for可以用来遍历数组内元素,对象内属性

例如data中有以下person对象数组

```javascript
persons: [
    {id: '001', name: 'origin', age: 20, gender: 'man'},
    {id: '002', name: 'jack', age: 22, gender: 'man'},
    {id: '002', name: 'gene', age: 24, gender: 'woman'},
]
```



用v-for实现遍历效果

```html
<ul>
    <li v-for="p in persons" :key="p.id">
        {{p.name + ' ' + p.age + ' ' + p.gender}}
    </li>
</ul>
```

这里的`:key`需要填入p的唯一标识

v-for中每个item会默认传入两个参数,一个是要遍历的每个item项,还要一个是该item的下标,因此key中可以填入该下标值

```html
<ul>
    <li v-for="{p,index} in persons" :key="index">
        {{p.name + ' ' + p.age + ' ' + p.gender}}
    </li>
</ul>
```



## 虚拟dom的对比

每次页面发生变化时,vue会将初始数据所对应的虚拟dom和新数据所对应的虚拟dom进行比较,比较的依据是每个元素的`:key`,vue会将两个虚拟dom中key相同的拿出来,分别对比其内部的元素是否相同,相同就复用初始数据的真实dom,不同就依据新数据的虚拟dom新生成一个dom

当我们不显示地指定key时,key默认为元素的index



## 列表过滤

列表过滤就是模糊查询

用到的前置知识:

1. 数组的filter方法,传入一个函数,该函数返回一个boolean,filter会依次取出数组内的元素并调用传入的函数,将所有返回true的元素形成一个新数组返回
2. 箭头函数:普通函数的this取决于函数的调用者,箭头函数的this取决于该函数的父级对象所处的环境的this对象;对于vm管理的函数,要定义成普通函数,这样this就是mv,如果定义成箭头函数,那么this就变成了window;对于非vm管理的函数,例如filter内传入的函数,要定义成箭头函数,这样this就是vm,如果定义成普通函数,那么this就是window

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="../js/vue.js"></script>
    <title>模糊查询</title>
</head>
<body>
<div id="root">
    <input type="text" placeholder="搜索用户" v-model="key">
    <ul>
        <li v-for="p in queryArr">
            {{p.name}}-{{p.age}}-{{p.gender}}
        </li>
    </ul>
</div>
<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            persons: [
                {name: '马冬梅', age: 19, gender: '女'},
                {name: '周冬雨', age: 20, gender: '女'},
                {name: '周杰伦', age: 21, gender: '男'},
                {name: '温兆伦', age: 22, gender: '男'}
            ],
            key: '',
            //queryArr: []
        },
        //使用computed实现
        computed: {
            //使用箭头函数,可以实现功能
            queryArr: function () {
                return this.persons.filter((person) => {
                    return person.name.indexOf(this.key) != -1
                })
            }
            //使用普通函数,无法实现功能
            // queryArr: function () {
            //     return this.persons.filter(function (person) {
            //         console.log('普通函数的this是: ' + this)
            //         return person.name.indexOf(this.key) != -1
            //     })
            // }
        },
        //使用watch实现
        watch: {
            // key: {
            //     immediate: true,
            //     //手写filter方法
            //     handler: function () {
            //         this.queryArr = []
            //         for (let p of this.persons) {
            //             if (p.name.indexOf(this.key) != -1) {
            //                 this.queryArr.push(p)
            //             }
            //         }
            //     },
            		   //使用filter方法
            //         handler: function () {
            //             this.queryArr = this.persons.filter((person) => {
            //                 return person.name.indexOf(this.key) != -1
            //             })
            //         }
            // }
        }
    })
</script>
</body>
</html>
```





# vue监测数据的原理

vue会自动监测data中所有层次的数据



**监测对象**

vue监测数据原理的简单实现

```javascript
//模拟我们传给vue实例的data对象
let data = {
    name: 'origin',
    age: 20
}

//vue底层创建一个data对象的代理对象observer
let observer = new Observer(data)
//模拟vue对象实例
let vm = {}
//将data的代理对象observer赋值给vm的_data
vm._data = observer

//Observer构造函数,传入data
function Observer(obj) {
    //得到obj的所有属性
    let keys = Object.keys(obj)
    //遍历每个属性,并给当前observer对象实例添加该属性
    keys.forEach((k) => {
        Object.defineProperty(this, k, {
            get: function () {
                return obj[k]
            },
            set: function (val) {
                //vue在这里实现页面的渲染
                console.log('vue在这里实现重新渲染页面')
                obj[k] = val
            }
        })
    })
}
```



**set方法**

`Vue.set(target, porpertyName, value)`方法可以给data中的某个对象添加一个属性,例如这里给data中的friends对象添加一个对象

```javascript
const vm = new Vue({
    data: {
        friends: {}
    }
})
Vue.set(vm.friends, 'origin', {name: 'origin', age: 20})
```

注意set方法只能给data中的某个对象添加属性,而不能直接给vm对象实例或vm._data添加属性



**监测数组**

vue对于数组内的对象并没有做getter和setter,而是对数组的一些常用操作进行了重写,例如push方法,vue的push方法会先调用Array的push方法然后再进行一次页面的重新渲染从而实现响应式,而对于数组内的对象的属性,还是有getter和setter的,因此

```javascript
//friends是一个对象数组
vm.friends[0] = {name: 'origin', age: 20}//无效,数组内对象无getter和setter
vm.friends.splice(0, 1, {name: 'origin', age: 20})//有效,vue对数组常用方法做了响应式重写
vm.friends[0].name = 'origin'//有效,数组内对象的属性有getter和setter
```





# 收集表单数据

首先明确,v-model收集的是value值



因此,对于text和password,我们输入的值就是value

对于redio,其默认没有value,因此需要我们手动配置value

对于checkbox,如果没有配置value或者在data中其初始值不是一个数组,那么收集的是checked,如果配置了value,并且在data中其初始值是一个数组,那么收集的就是value组成的数组



**v-model的三个修饰符**

- lazy 失去焦点后再收集数据
- number 将输入的字符串类型数据转为有效的数字类型数据
- trim 消除输入数据中的首尾空格空格





# 过滤器

过滤器filters与el,data等同级

在需要过滤的对象后加上`|filterName`来进行过滤,实质上就是一个函数

```html
<div class="root">{{time | format}}</div><!--将time传给format过滤器-->
<script type="text/javascript">
    // Vue.filter('format', function () {
    //     return dayjs().format('YYYY年MM月DD日HH时mm分ss秒');
    // })
    new Vue({
        el: '.root',
        data: {
            time: '1659427426828'
        },
        filters: {
            <!--过滤器函数的返回值就是过滤后的值-->
            format: function (value) {
                return dayjs().format('YYYY年MM月DD日HH时mm分ss秒');
            }
        }
    })
</script>
```

过滤器可以链式编程,顺序从左往右,即原始属性传给第一个过滤器,第一个过滤器的返回值传给第二个过滤器,以此类推

```html
<div class="root">{{time | format1 | format2 | format3}}</div>
```



# 内置指令



## v-text

t-text向所在节点渲染文本内容

```html
<!--给box添加data中的mes文本数据-->
<div class="box" v-text="mes"></div>
```





## v-html

v-html作用同v-text,但是v-text不支持结构解析,即所有内容都会被解析为文本,v-html支持结构解析,例如解析文本中的html标签





## v-cloak

没有值,本质是一个特殊属性,Vue实例创建完毕并接管容器后,会删掉v-cloak属性



## v-once

v-once没有值,其所在节点在初次动态渲染完成后就视为静态内容了视为静态内容了



## v-pre

没有值,使vue跳过所在节点的解析过程,如果某个节点中没有动态资源时,就可以用v-pre修饰





# 自定义指令

例如我们在标签中设置一个v-big指令,可以将值扩大十倍后显示

```html
<span v-big="n"></span> <br> <br>
```



给vue对象实例添加一个directives配置项,指令的函数式写法如下,默认传入两个参数,element表示指令所在的标签,bind为一个包含指令信息的对象,其中bind.value为指令的值

```javascript
directives: {
    big: function (element, bind) {
        //使用原生js来设置指令值和标签值之间的关系
        element.innerText = bind.value * 10;
    }
}
```



该指令函数被调用的时机:

- 指令与标签初次成功绑定时
- 指令所在的模板被重新解析时



上面的函数式写法是指令的简化写法,完整写法是将指令写成一个对象

```javascript
directives: {
    myDir: {
        bind: function () {//指令与指令所在元素成功绑定时调用
        }
        inserted: function () {//指令所在元素被插入页面后调用
        }
        update: function () {//指令所在的模板被重新解析时调用
        }
    }
}
```





注意点:

- 指令的声明不支持驼峰命名,把驼峰改成`-`
- 指令中的所有函数的this都是window





# 生命周期函数



## mounted

当vue完成模板的解析并把初始的真实的DOM元素放入页面后(挂载完毕)会调用mounted方法,我们可以自定义该方法的实现

```javascript
const vm = new Vue({
    el: '#root',
    data: {},
    mounted: function () {}
})
```

注意mounted函数在挂载后被调用,挂载指的是页面的初始DOM第一次放到页面上时,即mounted方法只会调用一次





## 完整生命周期函数

- **boforeCreate** 调用该方法时,data,methods中的数据还未加载到vm中
- **created** 调用该方法是,data,methods中的数据已经加载到vm中
- **beforeMount** 调用该方法时,虚拟dom已经生成但是还未生成真实dom,页面上显示的是未编译的dom,在这里对dom的操作最终是无效的
- **mounted**挂载完成后,调用mounted方法,此时页面呈现的是经过编译后的dom
- **beforeUpeate** 数据已经更新,但页面还未更新时调用该方法
- **updated** 数据和页面都已更新完毕时调用该方法
- **beforeDestroy** vm即将销毁之前调用该方法,此处对dom的操作仍然有效,但是对数据的修改不会再触发页面的更新
- **destroy** vm销毁之后调用该方法

 

