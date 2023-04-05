# vuex简介

vuex是一个vue插件,封装了一套便于组件间通信的功能



# 基本使用

**配置store**

使用了vuex插件后,vm中就需要额外配置一个配置项,叫做`store`

官方建议我们在src下创建`store/index.js`目录结构,在index.js中创建store对象

store对象包含三个必须属性:actions, mutaions, state

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

//由于use语句需要在Store对象被创建之前执行,因此不能在main.js中使用use语句,而要放在这里
Vue.use(Vuex)

const actions = {} //响应组件的动作
const mutations = {} //操作数据
const state = {} //数据
const getters = {}

//创建并暴露strore对象
export default new Vuex.Store({
    actions,
    mutations,
    state,
    getters,
})
```



在main.js中引入store对象并给vm添加该配置项

```javascript
import Vue from 'vue'
import App from './App.vue'
import store from './store/index'//引入
Vue.config.productionTip = false

new Vue({
    render: h => h(App),
    store,//添加store配置项
    beforeCreate: function() {
        Vue.prototype.$bus = this
    }
}).$mount('#app')
```



从前我们的数据一定是配置在某个实际组件中,使用了vuex插件后,可以将需要被多个组件共享/读写的数据配置在store对象的state属性中,且state中的数据是响应式的

```javascript
const state = {
        sum: 0,
    }
```



**读取数据**

在组件中读取数据,则直接读取`this.$store.state.数据名`

```vue
<template>
    <h1>{{$store.state.sum}}</h1>
</template>
```



**操作数据**

在组件中操作数据,首先需要调用`dispatch(方法名,参数)`方法,该方法会帮我们调用actions中对应的方法

```javascript
this.$store.dispatch('add', 1)
```



actions中的方法完成的是操作数据前的一些业务逻辑,默认收到一个context参数,这个context中包含我们可能会用到的store上的函数,在actions中完成某些业务操作后,调用store的commit方法,commit方法会帮我们调用mutations中对应的方法

```javascript
const actions = {
        add(context, value) {
            //在这里实现一些业务逻辑
            context.commit('ADD', value)
        }
    } //响应组件的动作
```



mutations中的方法完成的是对state中的数据的操作,默认传入state参数

```javascript
const mutations = {
        ADD(state, value) {
            state.sum += value
        }
    } //操作数据
```



注意点:

- 总结流程:在组件中调用dispatch方法,传入对应方法名和参数,dispatch方法调用对应的action层方法,action方法中通过commit方法再调用对应的mutation层方法,在mutation方法中完成对state中数据的操作,类似于java后端中的domain->dao->service思想
- 由于dispatch,commit等方法都是在store对象上的,因此在组件中可以跳过action方法,直接调用commit方法进入mutation层





# getters

getters是store对象中的一个非必须的配置项,用于对state中的数据进行加工,getters中的每个函数通过返回值的形式对应一个对象,函数默认传入state参数

```javascript
const getters = {
    bigSum(state) {
        return state.sum *= 10
    }
}
```

获取getters中的数据通过`$store.getters.数据名`



# mapState&mapGetters

mapState和mapGetters是两个函数,用于帮我们生成state和getters中的数据的对应的计算属性,从而在读取state和getters中的数据时不用再加繁琐的前缀



**引入**

```javascript
import {mapState, mapGetters} from 'vuex'
```



**使用**

函数的返回值就是state中data1和data2所对应的计算属性函数,将其加入computed配置项中即可

```javascript
computed: {
    //传入对象形式, ...表示将该对象中的属性加入当前对象
    ...mapState({data1: 'data1', data2: 'data2'})
    //传入数组形式
    ...mapState(['data1', 'data2'])
}
```

以上配置等价于生成计算属性data1和data2,其值分别是state中的data1和data2

mapGetters同理





# mapMutations&mapActions

同理于mapState和mapGetters,mapMutations和mapActions是帮我们映射dispatch方法和commit方法

```javascript
methods: {
    ...mapActions({add: 'add'})//生成名为add的函数,函数中调用dispatch('add', params)
    ...mapMutations({add2: 'add2'})//生成名为add2的函数,函数中调用commit('add',params)
}
```

需要传入的参数在调用函数的时候传入

```html
<button @click="add(1)">加</button>
```

同上,这两个函数既可以传入对象,也可以传入数组





# 模块化

vuex模块化即将不同的模块文件拆分



在store文件夹下创建两个模块,`module1.js`,`module2.js`

```javascript
//module1.js
export default {
    namespaced: true,//模块文件中需要加上这一句
    actions: {},
    mutations: {
        ADD(state, value) {
            state.number += value
        },
        SUB(state, value) {
            state.number -= value
        }
    },
    state: {
        number: 0
    }
}
```



```javascript
export default {
    namespaced: true,
    actions: {},
    mutations: {
        REVERSE(state) {
            if (state.string === 'hello,world.')
                state.string = 'fuck you,world.'
            else
                state.string = 'hello,world.'
        }
    },
    state: {
        string: 'hello,world.'
    }
}
```



在store文件夹下的index.js中创建store对象,引入module1和module2

```javascript
export default new Vuex.Store({
    modules: {
        module1: module1,
        module2: module2
    }
})
```



使用map映射函数时,需要传入模块名

```javascript
...mapState('module1', ['number']),
...mapState('module2', ['string'])
```



```javascript
...mapMutations('module2', {reverse: 'REVERSE'})
```



