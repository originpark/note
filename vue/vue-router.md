#### vue-router简介

路由英文route,路由就是一组key-value的对应关系,多个路由需要经过路由(router)器的管理

vue-router是vue中的路由器插件,vue中的路由即路径和组件之间的对应关系



# 基本使用

下载vue-router插件

在src下创建router目录,新建index.js

```javascript
import VueRouter from 'vue-router'//引入vue-router
import About from '../components/About'
import Home from '../components/Home'

export default new VueRouter({
    routes: [{//配置路由,即路径和组件的对应关系
            path: '/About',
            component: About
        },
        {
            path: '/Home',
            component: Home
        }
    ]
})
```



在main.js中,为wm对象实例添加router配置项

```javascript
import Vue from 'vue'
import App from './App.vue'
import VueRouter from 'vue-router'//引入vue-router插件
import router from './router/index'//引入我们配置好的router对象实例
Vue.config.productionTip = false


Vue.use(VueRouter)//使用vue-router插件
new Vue({
    render: h => h(App),
    beforeCreate: function() {
        Vue.prototype.$bus = this
    },
    router: router//添加router配置项
}).$mount('#app')
```



vue-router库提供的router-link标签,用于实现点击更改路径的功能,最终会被转为a标签

```html
<router-link class="list-group-item" active-class="active" to="/About">About</router-link>
<router-link class="list-group-item" active-class="active" to="/Home">Home</router-link>
```

to属性表示要切换到的路径,active-class属性表示该标签被点击后激活的样式



在需要展示不同组件的位置使用router-view标签

```html
<router-view></router-view>
```



注意点:

- router-view的工作原理是创建和销毁组件
- 通过router-view创建出来的组件会多两个属性,$route和$router,$route包含自身路由的一些信息,$router是路由器对象,所有组件都由唯一一个路由器对象管理
- 通过路由创建的组件叫做路由组件,路由组件通放在pages目录,普通组件放在components目录





# 多级路由

即一个路由下还可以有多个子路由

子路由在配置router时使用children属性配置

```javascript
routes: [{
    path: '/module01',
    component: Module01,
    children: [{
        path: 'sub01',
        component: Sub01
    },
    {
        path: 'sub02',
        component: Sub02
    }]
    },
    {
        path: '/module02',
        component: Module02,
    },
]
```



在router-link中,to属性中的路径也需要使用多级形式

```html
<router-link active-class="active" to="/module01/sub01">sub01</router-link>
<router-link active-class="active" to="/module01/sub02">sub02</router-link>
```



# query参数

即在router-link的to属性中的路径中可以传入query参数

to的字符串传参方式

```html
<router-link :to="`/module01/sub01/detail?msg=${detail01}`">detail01</router-link>
```

to的对象传参方式

```html
<router-link to="{
    path: '/module01/sub01/detail',
    query: {
        msg: detail02
    }
}">
	detail02
</router-link>
```

这样在detail组件中就能接收到一个msg参数,在detail组件中从组件自身的$route对象的query属性中读取参数

```html
{{$route.query.msg}}
```





# 命名路由

在配置router对象时,可以为route对象配置一个name属性,这样,在router-link的to的对象写法中,就可以传入name值来代替path值



# params参数

params参数即路径参数

在配置router对象时给route添加路径参数

```javascript
children: [{
    name: 'msg',
    path: 'detail/:msg',//用:标识一个路径参数
    component: Detail
}]
```



在router-link中使用路径参数

字符串写法

```html
<router-link :to="`/module01/sub01/detail/${detail01}`">detail01</router-link>
```

对象写法,注意如果使用的是params参数,那么对象写法中只能使用name,不能使用path

```html
<router-link :to="{
    name: 'msg',
    params: {
        msg: detail02
    }
}">
    detail02
</router-link>
```



获取参数时,从$route对象的params属性中获取

```javascript
{{$route.params.msg}}
```



# 路由的props配置项

在配置router时,可以为route配置一个props配置项,用于将route接收到的参数以props的形式传给组件

props配置项可以是bool值,为true表示将所有的params参数以props的形式传给对应的路由组件

```javascript
name: 'msg',
path: 'detail/:msg',
component: Detail,
props: true//表示将params参数msg以props的形式传给组件
```

在对应路由组件中声明props,就可以通过使用props的方式直接使用参数

```javascript
props: ['msg']
```



props为bool值时只能处理params参数,props也可以是一个函数,为函数时则可以处理params和query参数

```javascript
props($route) {//默认传入$route对象
    return {//以返回值的形式配置props参数
         msg: $route.params.ms     
    }
}
```



# router-link的replace属性

浏览器的历史记录的写入有两种方式,一是圧栈,二是直接替换,默认情况下是圧栈方式

在router-link中加入replace属性,则浏览器的历史记录的写入方式就为替换方式



# 编程式路由导航

router-link最终会被转换为a标签,而我们的路由并不总是用a标签

$router原型对象中提供了一些方法来完成路由操作



将router-link换为按钮,同时绑定点击事件

```html
<button @click="showDetail(detail01)">detail01</button>
<button @click="showDetail(detail02)">detail02</button>
<button @click="showDetail(detail03)">detail03</button>
```



在事件函数中调用$router对象的push方法,该方法传入的配置对象同to属性,表示圧栈式历史记录写入

```javascript
showDetail(detail) {
    this.$router.push({
        name: 'msg',
        params: {
            msg: detail
        }
    })
}
```

$router的`replace`方法类似于push方法,只不过是替换式历史记录写入



# 路由缓存

router-view中显示的路由组件的切换实际上是组件的创建与销毁的过程

例如,路由组件中有输入框,那么切换路由后输入框就被销毁,其内的内容就无法缓存

用keep-alive标签包裹router-view标签,可以让路由组件在切换时不销毁组件,实现组件缓存

```html
<router-link active-class="active" to="/module01/sub01">sub01</router-link>
<router-link active-class="active" to="/module01/sub02">sub02</router-link>
<keep-alive include="Sub01">
    <router-view></router-view>
</keep-alive>
```

include表示要缓存的组件,例如此处缓存sub01组件,不写include则表示缓存此keep-alive中的所有组件

如果要缓存多个组件,则include值为数组`:include="['c1', 'c2', 'c3']"`



# activated&deactivated

activated和deactivated是路由组件特有的两个声明周期钩子,分别在组件被激活和失活时调用



# meta

meta是route对象中的一个配置项,在配置route时可以在里面放我们想放的任何内容



# 路由守卫

路由守卫即对路由组件的访问做一些权限处理,例如有些组件在用户未登录时不能展示

在配置router时,不立即暴露router对象,而是先设置路由守卫,再暴露

```javascript
//定义router
const router = {}
//设置路由守卫
router.beforeEach((to, from, next) => {})
//暴露router
export default router
```



**全局前置路由守卫**

即beforeEach方法,方法中传入一个回调函数,该函数在路由器初始化,以及路由切换之前调用,默认传入三个参数,to表示要切换到的route对象,from表示切换前的route对象,next为放行函数,只有调用next方法才会真正切换路由

```javascript
router.beforeEach((to, from, next) => {})
```



**全局后置路由守卫**

即afterEach方法,回调函数在路由器初始化,以及路由切换成功之后调用,默认传入to和from

```javascript
router.afterEach((to, from) => {})
```



**独享路由守卫**

即beforeEnter方法,配置在route中,由该route独享,在要切换到该路由之前调用

```javascript
routes: [
    {
        path: '/module01',
        beforeEnter(to, from, next) {}
    }
]
```



**组件内路由守卫**

组件内路由守卫是组件的配置项

`beforeRouteEnter((to, from, next) => {})` 进入路由之前调用 

`beforeRouteLeave(to, from) ` 离开路由之前调用
