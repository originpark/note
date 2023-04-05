# 创建vue3工程

可以使用vue脚手架,执行`vue create 项目名`创建工程

也可以使用vite创建,具体看vite官网



# main.js

main.js的变化

```javascript
import { createApp } from 'vue'//createApp是一个工厂函数
import App from './App.vue'
//调用createApp函数返回一个app对象实例(精简版的vm对象实例),并挂载app组件
createApp(App).mount('#app')
```



# setup

setup是app对象实例中的一个配置项,用以取代vue2中的data,methods等配置项,其值为一个函数,我们可以在函数体中定义任意数据和方法,最后以返回值的形式将他们返回,这样这些数据都能在模板中使用

```javascript
export default {
  name: 'App',
  setup() {
    let name = 'origin'
    let age = 20
    return {
      name,
      age
    }
  }
}
```

注意点:

- setup函数中的this是undefined
- setup函数调用的时机早于beforeCreate
- setup的两个参数
  - props: 值为对象,包含组件外部传递过来,且在组件内的props中声明了的属性
  - context 上下文对象
    - attrs: 值为对象,包含组件外部传递过来,但没有在组件内的props中声明的属性
    - slots: 收到的插槽内容
    - emit: 触发自定义事件的函数,等价于vue2中的$emit函数



# ref

在setup函数中定义的普通数据是无响应式的,如果想定义响应式数据,则需要使用ref函数来包装数据,数据ref函数包装后,返回的是一个`RefImpl`对象实例,该对象实例的value属性值就是被封装的数据,该value属性通过defineProperty方法实现了响应式

```vue
<script>
import { ref } from 'vue'//引入ref函数
export default {
  name: 'App',
  setup() {
    //使用ref函数封装响应式数据,返回RefImpl对象实例
    let name = ref('origin')
    let age = ref(20)
    function change() {
      //通过.value属性读写数据值
      name.value = 'jack'
      age.value = 30
    }
    return {
      name,
      age,
      change
    }
  }
}
</script>
```

在模板中使用RefImpl对象,vue会自动获取其value值,因此无需手动添加.value

```vue
<template>
  <h1>name: {{name}}</h1>
  <h1>age: {{age}}</h1>
  <button @click="change">change</button>
</template>
```



注意:

- ref包装基本数据类型,返回RefImpl对象实例,其value值为被包装的数据,且使用getter和setter来实现响应式

- ref包装对象类型,同样返回RefImpl对象实例,其value值为一个proxy对象(同样实现了响应式),因此对于`let person = ref({name: 'origin', age: 18})`,获取name值时使用`person.value.name`即可

  

# reactive

> ref函数可以包装基本数据类型和对象类型,经ref包装的数据返回一个RefImpl对象实例,如果源数据是基本数据类型,那么该RefImpl的value属性是通过defineProperty方法实现响应式的源数据,如果源数据是对象,那么RefImpl对象的value属性就为一个proxy对象(使用了reactive函数进行封装);

reactive函数与ref函数作用类似,也是包装数据实现响应式,但是reactive函数只能包装对象,不能包装基本数据类型

reactive函数包装对象类型直接返回一个proxy对象实例,该proxy对象就是源数据的一个代理对象,可以直接通过该代理对象对源对象进行增删改查操作



# vue3响应式原理

> 在vue2中,使用defineProperty方法来实现响应式,因此如果我们想要给某个对象新增一个属性或删除一个属性,必须使用get方法,否则添加的数据就是没有实现响应式的数据,且对数组的操作也必须使用操作数组的方法来实现响应式

vue3底层使用window中内置的`Proxy`构造函数来实现响应式,Proxy构造函数传入两个参数,一是被代理的对象,二是对被代理对象进行增删改查的函数

```javascript
//被代理对象
const person = {
    name: 'origin',
    age: 20
}

//代理对象
cosnt personProxyObj = new proxy(person, {
    get(target, propName) {//被代理对象的某个属性被读取时调用,target为被代理对象,propName为要读取的属性名
        return Reflact.get(target, propName)//等价于return target[propName]
    },
    set(target, propName, value) {//被代理对象的某个属性被修改或给被代理对象添加属性时调用
        Reflact.set(target, propName, value)//等价于target[propName] = value
        console.log('在这里进行响应式操作')
    },
    deleteProperty(target, propName) {//被代理对象的某个属性被删除时调用
        bool isSuccess = Reflact.deleteProperty(target, propName)
        console.log('在这里进行响应式操作')
        return isSuccess
    }
})
```



# computed

vue3中的计算属性同ref和active一样,也是以函数的形式使用,也需要引入

```vue
<script>
import { reactive, computed } from 'vue'//引入计算属性函数computed
export default {
  name: 'App',
  setup() {
    let person = reactive({
      firstName: 'zhang',
      lastName: 'san'
    })
    let fullName = computed(() => {//调用computed函数,表示其返回值是一个计算属性
      return person.firstName + '-' + person.lastName
    })
    return {
      person,
      fullName
    }
  }
}
</script>
```



# watch

watch同样是以函数的方式使用



**监视ref封装的单个数据**

data为要监视的数据

```javascript
watch(data, (newValue, oldValue) => {})
```



**监视ref封装的多个数据**

将要监视的多个数据放在数组中传入,handler函数中传入的newValues和oldValues也是数组,第三个参数为watch的配置对象

```javascript
watch([data1, data2], (newValues, oldValues) => {}, {deep: true})
```



**监视reactive封装的对象**

同监视ref封装的单个数据,但是无法获取正确的oldValue,且默认开启深度监视,deep配置无效



**监视reactive封窗的对象中的某个属性**

将要监视的属性以函数返回值的形式返回

```javascript
watch(() => person.name, (newValue, oldValue) => {})
```



**监视reactive封装的对象中的某些属性**

```javascript
watch([() => person.name, () => person.age], (newValues, oldValues) => {})
```



**监视reactive封装的对象中的一个对象属性**

默认不开始深度监视,deep配置有效



# watchEffect

watchEffect同样是个函数,只需传入一个回调函数,该回调函数中使用了哪个数据,watchEffect就监视该数据

```javascript
watchEffect(() => {})
```



# 生命周期

基本与vue2相同,区别是将beforeDestroy换成了beforeUnmount,将destroy换成了unmounted

在vue3中,这些钩子可以以配置项的形式声明,也可以在setup中以api的方式声明

- `beforeCreate/create` ==> `setup()`
- `beforeMount` ==> `onBeforeMount()`
- `mounted` ==> `onMounted`
- `beforeUpdate` ==> `onBeforeUpdate`
- `updated` ==> `onUpdated`
- `beforeUnmount` ==> `onBeforeUnmount`
- `unmounted` ==> `onUnMounted`



# hook

将对某个对象的操作封装为一个函数,写在一个单独的js文件中,该文件就是一个hook

我们可以在src下创建hooks目录,将所有的hook放在这里,需要使用某个hook时,import该hook即可



# toRef/toRefs

toRef函数作用是创建一个refImpl对象,其value值指向另一个对象中的某个属性

语法为`const name = toRef(person, 'name')`,表示name指向person对象的name属性,且name是一个响应式数据

toRefs函数表示将某个对象的所有属性都做toRef处理,将处理后的所有属性包含在一个对象中返回



# 其他组合式api

## shallowRef/shallowReactive

shallowReactive是reactive的替代函数,表示浅层次的响应式,即只为被封装的对象的第一层属性实现响应式

shallowRef是ref的替代函数,区别在于当shallowRef封装对象时,其value值不再是proxy对象,而是普通的object,即其value无响应式



## readonly/shallowReadonly

readonly是一个函数,接收一个响应式数据作为参数,将该数据封装后返回,返回的数据就是只读的,其内部属性不能被修改

shallowReadonly封装的对象只有第一层属性为只读



## toRaw/markRaw

toRaw函数传入一个被reactive函数封装过的响应式对象,将其还原为原始对象并返回

当我们给响应式对象添加一个属性时,默认该属性也是响应式的,可以使用markRaw函数封装要添加的属性,从而实现添加非响应式数据



## customRef

即自定义ref

```vue
<script>
import { customRef } from 'vue'//引入customRef
export default {
  name: 'App',
  setup() {
    function myRef(value) {//自己定义一个Ref函数
      return customRef((track, trigger) => {//调用customRef函数并返回,传入一个函数
        return {//传入的函数需要返回一个对象,该对象包含get和set方法
          get() {//当读取myRef封装的数据时调用,读取到的值为get的返回值
            track()//通知vue追踪数据的变化
            return value
          },
          set(newValue) {//当修改myRef封装的函数时调用
            value = newValue
            trigger()//通知vue重新解析模板
          }
        }
      })
    }
    let keyWord = myRef('hello') 
    return { keyWord }
  }
}
</script>
```



# provide&inject

用于父组件向后代组件中传递数据

在父组件中调用provide函数给给后代组件传递数据

```javascript
provide('person', person)
```

在后代组件中获取数据

```javascript
person = inject('person')
```





# 响应式数据判断

- `isRef` 检查一个值是否是RefImpl对象
- `isReactive` 检查一个对象是否由reactive函数封装
- `isReadonly`
- `isProxy`



# suspense

异步引入

```vue
<script>
	import { defineAsyncComponent } from 'vue'
    //异步引入Child组件
    const Child = defineAsyncComponent(() => import('./components/Child'))
</script>
```





```html
<Suspense>
	<template v-slot:default>//异步组件加载完成显示该插槽
        <Child></Child>
    </template>
    <template v-slot:fallback>//异步组件加载中时显示该插槽
    	<h3>加载中</h3>
    </template>
</Suspense>
```

