# 组件分类

- 非单文件组件: 一个文件中包含有n个组件
- 单文件组件: 一个文件中只包含一个组件

通常情况下都是使用单文件组件





# 非单文件组件



## 基本使用



首先创建组件

```vue
const header = Vue.extend({
	name: 'lalal',//使用name来指定组件在开发者工具中的名字
    template: `//模板,即html结构
            <div>
              <h1>{{mes}}</h1>
            </div>
        `,
    data: function () {//data对象以函数返回值的形式返回
        return {
            mes: 'this is a header'
        }
    }
})
```



在vm中注册组件(局部注册,也可以使用`Vue.component('组件名', 组件对象)`来注册一个全局组件)

```javascript
new Vue({
    el: '#root',
    components: {
        origin:header//将header组件命名为origin
    }
})
```



使用组件,在html中加入以组件名命名的标签即可使用组件

```html
<body>
    <div id="root">
        <origin></origin>
    </div>
</body>
```





## 组件嵌套

通常情况,vm只管理一个叫做app的组件,app组件直接或间接管理其他所有组件





vm管理app组件

```
new Vue({
    el: '#root',
    template: `<app></app>`,
    components: {//vm管理app组件
        app
    }
})
```



app组件管理Outer组件

```
const app = Vue.extend({
    template: `<div>
    			   <Outer></Outer>
    			   <Outer></Outer>
    		   </div>`,
    components: {//app组件管理Outer组件
        Outer,
    }
})
```



Outer组件管理Inner组件

```
const Outer = Vue.extend({
    template: `
        <div>
            <div class="window">
                <Inner></Inner>
                <Inner></Inner>
                <Inner></Inner>
                <Inner></Inner>
                <Inner></Inner>
                <Inner></Inner>
            </div>
        </div>
    `,
    components: {
        Inner
    }
})
```



Inner组件

```
const Inner = Vue.extend({
    template: `<div class="box">{{info}}</div>`,
    data: function () {
        return {
            info: 'hello, world'
        }
    }
})
```





最终的层级情况

<img src="../../images/Snipaste_2022-08-03_17-27-19.png" style="zoom: 50%; margin-left: 0;">



## vueComponent

- 组件的本质是一个名为VueComponent的构造函数,vueComponent和vm是基本相同的
- 当我们使用组件时,例如`<myComponent></myComponent>`,vue在解析时会帮我们调用VueComponet构造函数创建该组件的对象实例
- 组件的this是VueComponent对象实例
- VueComponent原型对象的原型对象是Vue原型对象





# 单文件组件



## .vue文件结构

单文件组件即为一个.vue文件,.vue文件内包含三个主要标签

```vue
<template></template>

<script></script>

<style></style>
```





## 应用实例

创建Box.vue

```vue
<template>
  <div class="box"></div>
</template>

<script>
export default {//导出对象
  name: "Box",

}
</script>

<style scoped>
  .box {
    float: left;
    border: 1px solid black;
    height: 50px;
    width: 100px;
  }
</style>
```



创建Banner.vue

```vue
<template>
  <div class="window">
  </div>
</template>

<script>
  import Box from "./Box";//导入Box.vue中的默认导出对象Box

  export default Vue.extend({//这里的Vue.extend可省略(不写vue会自动帮我们调用)
    name: 'Banner',
    component: {
      Box,//Banner组件中嵌套Box组件
    }
  })
</script>

<style>
  .window {
    margin: 0 auto;
    border: 2px solid black;
    height: 300px;
    width: 500px;
  }
</style>
```





创建App.vue

```vue
<template>
  <Banner></Banner>
</template>

<script>
import Banner from "./Banner";

export default {
  name: "App",
  components: {Banner}
}
</script>

<style scoped>

</style>
```



创建main.js,在这里导入App模块

```javascript
import App from "./App";

new Vue({
    el: 'root',
    components: {
        App
    },
    template: `<App></App>`
})
```



创建index.html,在这里创建root容器,引入main.js

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="../../js/vue.js"></script>
    <title>index</title>
</head>
<body>
    <div id="root"></div>
    <script src="main.js"></script>
</body>
</html>
```





# 使用vue脚手架



## 创建项目

vue脚手架就是一个包(第三方js模块),通过 `npm install -g @vue/cli`下载



创建脚手架,执行`vue create 项目名`,此时就会创建出一个helloWorld项目



进入这个项目的根目录,执行`npm run serve`,就能开启一个服务器(模拟?),在8080端口访问到我们的项目





## render函数

这个项目里面的main.js,与我们之前写的有些不同

```javascript
import Vue from 'vue'

import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')
```



在项目中有一个node_models目录,该目录下有我们所需要的所有第三方库,vue库也包含其中,vue包的目录结构如下

<img src="../../images/Snipaste_2022-08-04_15-04-35.png" style="zoom: 50%; margin-left: 0;">

其中,dist目录下是各种类型的vue,原始版本是vue.js,包含vue核心和模板解析器,带有runtime修饰的vue是vue的精简版本,只包含vue核心,不包含模板解析器

这里的import Vue from 'vue'表示的是引入vue这个文件夹,这是一种精简写法,实际表示的是引入`vue.runtime.esm.js`,即实际引入的是一个不包含模板解析器的精简版vue,之所以这么做是因为模板解析器只有在写项目的时候才会用到,在项目正式发布后就不再需要了,因此通常情况下我们引入的都是不带模板解析器的vue

这样做就会带来一个问题,即模板无法解析,即我们写在vue配置对象中的template配置是无效的

为了解决这个问题,引入render函数,render函数是由vue帮我们调用的,并且会接收一个createElement参数,该参数是一个函数,传一个组件给这个函数,这个歌函数就会解析这个组件,因此,我们可以如下配置

```javascript
render: function(createElement) {
    return createElement(App);//使用createElement函数来解析App组件,代替template配置项
}
```

将createElement参数名简写为h,再把这个函数写为箭头函数的形式,就得到了精简版本

```javascript
render: h => h(App)//箭头函数只传一个参数则不用写括号,只有一句return语句则不用写大括号和return
```

注意,只有vm的模板才需要render函数代替解析,对于组件的template标签,有第三方库帮我们解析,所以组件中不需要使用render函数





# ref属性

给标签加上ref属性后,该标签对应的dom元素就会保存在vc的$refs属性中,就可以通过该属性获取我们添加ref属性的dom元素

```vue
<template>
    <div class="root">
        <School ref="s"></School>
        <button @click="show">点我获取上面的dom</button>
    </div>
</template>

<script>
    import School from './components/School.vue'

    export default {
        name: 'App',
        components: { 
            School
        },
        methods: {
            show: function () {
                console.log(this.$refs.s);
            }
        } 
    }
</script>
```





# props配置项

在使用组件标签的时候,还可以给组件传入参数,语法如下

```html
<template>
    <div class="root">
        <School name="origin" address="changsha" :age="22"></School>
    </div>
</template>
```



在组件中,使用props配置项来声明要接收的参数,有三种书写方式

```javascript
props: ['name', 'address', 'age']//简写形式

props: {//标记参数类型形式,如果传入参数的类型不匹配,就会有警告
    name: String,
    address: String,
    age: Number
}

props: {//最完整的写法
    name: {
        type: String,//类型要求
        required: true//是否必要
    },
    address: {
        type: String,
        default: 'changsha'//默认值
    },
    age: {
        type: Number,
        required: true
    }
}
```

然后就可以在模板中使用这些参数了

注意

- 每个prop最终会绑定到vc上,但不会绑定到_data中
- props的初始化顺序优先于data内数据的初始化顺序,因此可以在data中通过声明`obj = this.prop1`获取某个prop并赋值给data中的某个对象





# mixin配置项

首先将一些需要多个组件复用的配置项写在一个单独的js文件中

```javascript
export const m01 = {
    methods: {
        show: function() {
            console.log('hello');
        }
    },
    data: function() {
        return {
            a: 100
        }
    }
}
```



在需要引入这些配置项的组件中引入并使用mixins配置项注册这些配置项

```html
<script>
    import {m01} from '../mixin/m01'
    export default {
        name: 'School',
        data: function () {
            return {
                b: 200
            }
        },
        mixins: [m01]
    }
</script>
```



上面这种方式属于局部混合,全局混合需要在main.js中配置

```javascript
import {hunhe1, hunhe2} from './hunhe';
Vue.mixin(hunhe1);
Vue.mixin(hunhe2);
```





# 插件

定义插件,定义的插件中必须要包含install方法,vue会帮我们调用install方法,该方法默认传入Vue原型对象

```javascript
export default {
	install: function (Vue) {
        
    }
}
```



在main.js中使用插件

```javascript
import plugins from './plugins'	
Vue.use(plugins)
```





# scoped

组件中的style标签中的内容最后是被app组件汇总的,所有的css都会汇聚到一起,因此会有类名冲突的情况

scoped用于修饰组件中的style标签,表示该style只作用于当前组件

注意: app组件中的style会影响所有子组件





# 本地存储

localStorage保存的数据会存储在磁盘中,sessionStorage与localStorage类似,但是是在浏览器关闭后清除

```javascript
localStorage.setItem(key, value)
localStorage.getItem(key)
localStorage.remove(key)
localStorage.clear()//清空本地存储
```





# 自定义事件



## 绑定事件

**方式一**

给子组件标签(子组件vc对象实例)绑定一个自定义事件,这里的事件函数在methods配置项中定义

```html
<MyComponent @myEvent="myEvent"></MyComponent>
```



自定义事件定义在哪个组件上,就只能在哪个组件中触发该事件,使用$emit方法触发事件

```javascript
this.$emit('myEvent', params)
```



即,在父组件中定义事件函数,然后将事件绑定给子组件,在子组件中调用事件函数,这样可以实现将子组件的数据传给父组件



**方式二**

先先需要添加事件的子组件标签添加ref属性,使父组件拥有该子组件的vc对象实例

```html
<MyComponent ref="MyComponent"/>
```



在合适的位置给子组件绑定自定义事件

```javascript
this.$refs.MyComponent.$on('myEvent', eventMethod)
```



事件触发同方式一





## 解绑事件

给哪个组件绑定了事件,就在哪个组件中调用off方法解绑事件

```javascript
this.$off('myEvent');//解绑单个事件
this.$off(['myEvent1', 'myEvent2'])//解绑多个事件,传入数组
this.$off();//解绑该组件的所有自定义事件
```



## 原生事件

给组件标签绑定的事件默认都以自定义事件处理,即

```html
<MyComponent @click="show"></MyComponent>
```

这里的click会被解析为一个自定义事件

如果想要给组件标签绑定原生事件,可以使用.native事件修饰符

```html
<MyComponent @click.native="show"></MyComponent>
```

这样click事件就会传给该组件的根标签



# 全局事件总线

> 从前,我们使用的组件间的通信方式:
>
> 父组件->子组件: 父组件使用props给子组件传递数据
>
> 子组件->父组件: 父组件提供函数,在子组件中调用该函数并将数据作为参数,或者在父组件中给子组件标签绑定自定义事件,然后在子组件中触发事件函数,并将数据作为参数传递



全局事件总线:利用自定义事件完成任意两个组件间通信

首先在创建vue对象实例时,利用钩子将vue自身添加到Vue原型对象中,以`$bus`命名

```javascript
new Vue({
    render: h => h(App),
    beforeCreate: function() {//在数据还未加载到vm中时调用
        Vue.prototype.$bus = this
    }
}).$mount('#app')
```



在需要接收数据的组件中,利用钩子给`$bus`绑定一个自定义事件

```javascript
mounted() {//挂载完毕后调用
    this.$bus.$on('reciveData', this.reciveData)
}
```



在需要发送数据的组件中,在合适位置触发`$bus`的自定义事件,完成数据传递

```javascript
this.$bus.$emit('reciveData', data)
```



**注意点**

- 作为全局事件总线需要两个条件:一是其需要能被所有组件访问到,二是事件总线需要能够调用`$on`,`$off`等自定义事件方法,由于Vue原型对象上的数据能够被所有组件访问,并且自定义事件方法是定义在vue原型对象上的,因此考虑在Vue原型对象上添加一个vm对象实例,用该vm对象实例来充当全局事件总线
- 接收数据组件除了要给总线绑定事件,还需要在组件自身被摧毁之前解绑这些事件(使用beforeDestroy钩子)



# 消息订阅与发布

下载第三方包`pubsub-js`



数据接收方订阅消息

```javascript
const token = pubsub.subscribe('mesName', mesMethods)
```



数据发送放发布消息

```javascript
pubsub.publish('mesName', data)
```



发布消息后,自动调用订阅方的回调函数并将消息名和data作为参数传递

数据接收方取消订阅

```javascript
pubsub.unsubscribe(token)
```





# $nextTick

`$nextTick`是一个方法,传入一个函数,表示等下一次重新渲染页面后调用该函数



# 过渡/动画

先使用`transition`标签包裹我们需要做出过渡的标签,appear表示动画立即执行

```html
<transition name="hello" appear>
    <div class="box come" v-show="k"></div>
</transition>
```

动画触发的关键是v-show,也就是元素显示与隐藏的变化

当元素由显示变为隐藏时,vue提供三个css类

- v-enter 变化的起点样式
- v-enter-to 变化的终点样式
- v-enter-active 变化过程中的样式

当元素由隐藏变为显示时,vue同样提供三个css类

- v-leave
- v-leave-to
- v-leave-active

这些类由我们自己实现,vue帮我们维护



注意:

- css类名中的v可以匹配transition中的name属性从而实现针对性的css动画样式
- 如果transition中不止一个标签,则需要将transition替换为transition-group标签,并且内部的每个元素都要指定key属性



## 第三方动画库

[Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

```html
<transition appear 
    name="animate__animated animate__bounce"
    enter-active-class="animate__jello"
    leave-active-class="animate__fadeOutDown">
        <div class="box come" v-show="k"></div>
</transition>
```





# 插槽

## 默认插槽/具名插槽

在组件中使用`slot`标签声明一个插槽,相当于一个占位符

```vue
<template>
    <div>
        <h1>start</h1>
        <slot name="slot1"></slot>
        <slot name="slot2"></slot>
        <h1>end</h1>
    </div>
</template>
```



在父组件中,在子组件标签内部,给各个插槽添加实际内容

```vue
<template>
  <div>
    <test>
      <p slot="slot1">slot1</p>
    </test>
    <test>
      <span slot="slot2">slot2</span>
    </test>
  </div>
</template>
```

如果不指定name,就是默认插槽,否则就是具名插槽



## 作用域插槽

在声明插槽时将自建组件的数据传给插槽的使用者

```vue
<template>
    <div>
        <h1>start</h1>
        <slot :name="name"></slot>//将name作为参数传递
        <h1>end</h1>
    </div>
</template>
```



在父组件中,使用template标签表示此处要为子组件中的作用域插槽定义内容,slot-scope属性值为子组件插槽传过来的参数

```vue
<template>
  <div>
    <test>
      <template slot-scope="age">
        <h2>{{age.age}}</h2>
      </template>
    </test>
  </div>
</template>
```



