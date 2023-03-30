# vue3学习

## Ref全家桶

### 1.isRef

作用: 判断是不是一个ref对象

```javascript
import { ref, Ref,isRef } from 'vue'
let message: Ref<string | number> = ref("我是message")
let notRef:number = 123
const changeMsg = () => {
  message.value = "change msg"
  console.log(isRef(message)); //true
  console.log(isRef(notRef)); //false
  
}
```

### 2.shallowRef

- 作用： 创建一个跟踪自身.value变化的ref，但不会使其值也变成响应式
- 个人理解：对基本数据类型，比如说number，string 是可以监听到，Object这种则不可以监听到
- 修改对象其属性是非响应式的这样是不会改变的，但直接修改value则是可以监听到
- 建议： ref和shallowRef不能一块写，不然会影响shallowRef造成视图得更新

```vue
<template>
  <div>
    <button @click="change">change</button>
      {{ msg }}
  </div>
</template>
<script setup lang="ts">
import { ref,shallowRef } from 'vue'
type sampleObj = {
  name:string
}
  let msg = shallowRef<sampleObj>({name:'小何'})
  const change= ()=>{
    msg.value.name = '小小' //直接修改对象属性不可以监听到
    console.log(msg)
  }

</script>
<style scoped>
</style>

```

```vue
<template>
  <div>
    <button @click="change">change</button>
      {{ msg.name }}
  </div>
</template>
<script setup lang="ts">
import { ref,shallowRef } from 'vue'
type sampleObj = {
  name:string
}
  let msg = shallowRef<sampleObj>({name:'小何'})
  const change= ()=>{
    msg.value = { name:'小小' } //修改对象
    console.log(msg)
  }

</script>
<style scoped>
</style>
```

### 3.triggerRef 

- 作用：强制更新页面DOM
- 使用了这个方法，shallowRef也会改变值

```vue
<template>
  <div>
    <button @click="change">change</button>
      {{ msg }}
  </div>
</template>
<script setup lang="ts">
import { ref,shallowRef } from 'vue'
type sampleObj = {
  name:string
}
  let msg = shallowRef<sampleObj>({name:'小何'})
  const change= ()=>{
    msg.value.name = '小小'
    triggerRef(msg)  //强制视图更新
    console.log(msg)
  }

</script>
<style scoped>
</style>

```

### 4.ref

- 接受一个内部值并返回一个**响应式**且可变的 ref 对象。ref 对象仅有一个 `.value` property，指向该内部值。

### 5.customRef

- 自定义ref
- customRef 是个工厂函数要求我们返回一个对象 并且实现 get 和 set 适合去做防抖之类的

```typescript
function myRef<T = any>(value: T) {
  let timer:any;
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newVal) {
        clearTimeout(timer)
        timer =  setTimeout(() => {
          console.log('触发了set')
          value = newVal
          trigger()
        },500)
      }
    }
  })
}
```

## reactive全家桶

### 1. reactive

- 用来绑定复杂的数据类型 例如 对象 队组

- vue已经做了泛型约束 `T extends object`

- 不可以绑定普通类型，否则会报错

- 数组不可直接赋值，如直接赋值页面不会变化，因为会脱离响应式

  解决方案：

  方案1

  ```javascript
  
  let person = reactive<number[]>([])
  setTimeout(() => {
    //person = [1, 2, 3] //不可直接赋值
    person.push(...arr) // 方案1 使用push
    console.log(person);
    
  },1000)
  ```

  方案2

  ```javascript
  type Person = {
    list?:Array<number>
  }
  //使用对象包裹
  let person = reactive<Person>({
     list:[]
  })
  setTimeout(() => {
    const arr = [1, 2, 3]
    person.list = arr;
    console.log(person);
    
  },1000)
  ```

### 2. readonly

- 拷贝一份proxy对象设置为只读

```javascript
import { reactive ,readonly} from 'vue'
const person = reactive({count:1})
const copy = readonly(person)
//person.count++
copy.count++
```

### 3.shallowReactive

> 只能对浅层的数据 如果是深层的数据只会改变值 不会改变视图

## to全家桶

### 1.toRef

- 将原始对象转成响应式对象
- 但原始对象是非响应式的就不会更新视图，但数据是会变
- 如果原始对象是响应式，则会更新视图并改变数据

```vue
<template>
  <div>
    <button @click="change">change</button>
    {{ state }}
  </div>
</template>
<script setup lang="ts">
import { toRef } from 'vue'
const sampleObj = {
  name: '喵呼',
  age: 18
}
const state = toRef(sampleObj, 'age')
const change = () => {
  state.value++
  console.log(state)
}
</script>
<style scoped></style>
```

### 2.toRefs

- 批量创建ref对象，方便解构使用

```javascript
import { toRef,reactive, toRefs } from 'vue'
const sampleObj = reactive({
  name: '喵呼',
  age: 18
})
let {name,age } = toRefs(sampleObj)
age.value++
```

### 3.toRaw

- 将响应式对象转为普通对象

```javascript
import { toRef,reactive, toRefs } from 'vue'
const sampleObj = reactive({
  name: '喵呼',
  age: 18
})
const obj = toRaw(sampleObj)
```

## computed计算属性

> 计算属性就是当依赖的属性的值发生变化的时候，才会触发他的更改，如果依赖的值，不发生变化的时候，使用的是缓存中的属性值。

1. 函数形式

```javascript
import { computed, reactive, ref } from 'vue'
let price = ref(0)//$0
let m = computed<string>(()=>{
   return `$` + price.value
})
price.value = 500
```

2. 对象形式

```javascript
import { computed, ref } from 'vue'
let price = ref<number | string>(1)//$0
let mul = computed({
   get: () => {
      return price.value
   },
   set: (value) => {
      price.value = 'set' + value
   }
})
```

## watch侦听器

### watch

> `watch` 需要侦听特定的数据源，并在单独的回调函数中执行副作用

> watch第一个参数监听源
>
> watch第二个参数回调函数cb（newVal,oldVal）
>
> watch第三个参数一个options配置项是一个对象{
>
> immediate:true //是否立即调用一次
>
> deep:true //是否开启深度监听
>
> }

1. 监听单个和多个ref案例

```javascript
//watch(message, (newVal, oldVal) => { 单个值
watch([message,message2], (newVal, oldVal) => { //多个则为数组
    console.log('新的值----', newVal);
    console.log('旧的值----', oldVal);
},{
    immediate:true,
    deep:true
})
```

2. 监听reactive

```javascript
import { ref, watch ,reactive} from 'vue'
let message = reactive({
    nav:{
        bar:{
            name:""
        }
    }
})
watch(message, (newVal, oldVal) => {
    console.log('新的值----', newVal);
    console.log('旧的值----', oldVal);
})

//监听 单一值用函数返回对象的值
//let message = reactive({
//   name:"",
//   name2:""
//})
//watch(()=>message.name, (newVal, oldVal) => {
//    console.log('新的值----', newVal);
//    console.log('旧的值----', oldVal);
//})
```

## watchEffect高级侦听器

> 立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。
>
> 如果用到message 就只会监听message 就是用到几个监听几个 而且是非惰性 会默认调用一次

```javascript
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect(() => {
    //console.log('message', message.value);
    console.log('message2', message2.value);
})
```

1. 清除副作用

> 在触发监听之前会调用一个函数可以处理你的逻辑例如防抖

```javascript
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    //console.log('message', message.value);
    oninvalidate(()=>{
        
    })
    console.log('message2', message2.value);
})
```

2. 停止跟踪

> watchEffect 返回一个函数 调用之后将停止更新

```javascript
const stop =  watchEffect((oninvalidate) => {
    //console.log('message', message.value);
    oninvalidate(()=>{
    })
    console.log('message2', message2.value);
},{
    flush:"post",
    onTrigger () {
    }
})
stop()
```

3. 更多配置项

|          | pre                | sync                 | post           |
| -------- | ------------------ | -------------------- | -------------- |
| 更新时机 | 组件**更新前**执行 | 强制效果始终同步触发 | 组件更新后执行 |

> onTrigger  可以帮助我们调试 watchEffect

```javascript
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    //console.log('message', message.value);
    oninvalidate(()=>{
 
    })
    console.log('message2', message2.value);
},{
    flush:"post",
    onTrigger () {
        
    }
})
```

## 生命周期

> setup语法糖中 beforCreate和created没有这两个生命周期的，使用setup去代替

<img src="./assets/lifecycle.16e4c08e.png" alt="组件生命周期图示" style="zoom: 33%;" />

| 选项式API       | 组合式API         |
| --------------- | ----------------- |
| beforeCreate    | Not needed        |
| created         | Not needed        |
| beforeMount     | onBeforeMount     |
| mounted         | onMounted         |
| beforeUpdate    | onBeforeUpdate    |
| updated         | onUpdated         |
| beforeUnmount   | onBeforeUnmount   |
| unmounted       | onUnmounted       |
| errorCaptured   | onErrorCaptured   |
| renderTracked   | onRenderTracked   |
| renderTriggered | onRenderTriggered |
| activated       | onActivated       |
| deactivated     | onDeactivated     |

## 父子组件传参

>使用defineProps接收给的参数
>
>使用defineEmits派发一个事件

```javascript
//TS
defineProps<{
    title:string,
    data:number[]
}>()
//js
defineProps({
    title:{
        default:"",
        type:string
    },
    data:Array
})
const emit = defineEmits(['on-click'])
//ts
// const emit = defineEmits<{
//     (e: "on-click", name: string): void
// }>()
const clickTap = () => {
    emit('on-click', list)
}
```

## 动态组件

> 来回切换的场景，比如说tab界面

```vue
<!-- currentTab 改变时组件也改变 -->
<component :is="tabs[currentTab]"></component>
```



注意事项

> 在Vue2 的时候is 是通过组件名称切换的 在Vue3 setup 是通过组件实例切换的

> 如果你把组件实例放到Reactive Vue会给你一个警告runtime-core.esm-bundler.js:38 [Vue warn]: Vue received a Component which was made a reactive object. This can lead to unnecessary performance overhead, and should be avoided by marking the component with `markRaw` or using `shallowRef` instead of `ref`. 
> Component that was made reactive: 
>
> 这是因为reactive 会进行proxy 代理 而我们组件代理之后毫无用处 节省性能开销 推荐我们使用shallowRef 或者  markRaw 跳过proxy 代理

```vue
const tab = reactive<Com[]>([{
    name: "A组件",
    comName: markRaw(A)
}, {
    name: "B组件",
    comName: markRaw(B)
}])
```

## 异步组件

> 在大型项目中，我们可能需要拆分应用为更小的块，并仅在需要时再从服务器加载相关组件。

```javascript
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...从服务器获取组件
    resolve(/* 获取到的组件 */)
  })
})
// ... 像使用其他一般组件一样使用 `AsyncComp`

//ES动态模块也是返回一个Promise所以可以这样导入
//const AsyncComp = defineAsyncComponent(() =>
//  import('./components/MyComponent.vue')
//)
```

```javascript
const AsyncComp = defineAsyncComponent({
  // 加载函数
  loader: () => import('./Foo.vue'),

  // 加载异步组件时使用的组件
  loadingComponent: LoadingComponent,
  // 展示加载组件前的延迟时间，默认为 200ms
  delay: 200,

  // 加载失败后展示的组件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 时间限制，并超时了
  // 也会显示这里配置的报错组件，默认值是：Infinity
  timeout: 3000
})

```

> 搭配Suspense使用 - 还在实验性阶段

## Teleport传送组件

> vue3 新特性
>
> `<Teleport>` 是一个内置组件，它可以将一个组件内部的一部分模板“传送”到该组件的 DOM 结构外层的位置去。
>
> 使用to来指定目标,支持id class 等选择器
>
>  `<Teleport :disabled="isMobile">`
>
> 使用disabled禁用teleport

```vue
<button @click="open = true">Open Modal</button>

<!-- 使用to来指定目标 -->
<Teleport to="body"> 
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>

```

## 组合式 API：依赖注入

> provide 可以在祖先组件中指定我们想要**提供**给后代组件的数据或方法，而在任何后代组件中，我们都可以使用 inject 来接收 provide **提供**的数据或方法。 

父组件传递数据

```vue
<template>
    <div class="App">
        <button>我是App</button>
        <A></A>
    </div>
</template>
    
<script setup lang='ts'>
import { provide, ref } from 'vue'
import A from './components/A.vue'
let flag = ref<number>(1)
provide('flag', flag)
</script>
    
<style>
.App {
    background: blue;
    color: #fff;
}
</style>
```

子组件接受

```vue
<template>
    <div style="background-color: green;">
        我是B
        <button @click="change">change falg</button>
        <div>{{ flag }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { inject, Ref, ref } from 'vue'
 
const flag = inject<Ref<number>>('flag', ref(1))
const change = () => {
    flag.value = 2
}
</script>
    
<style>
</style>
```

> Tips:你如果传递普通的值 是不具有响应式的 需要通过ref reactive 添加响应式
>
> 使用场景
>
> 当父组件有很多数据需要分发给其子代组件的时候， 就可以使用provide和inject。

## Event Bus

## TSX

## 自定义指令directive

## 自定义Hooks

## Vue3定义全局函数和变量

## Vue3插件

## Evnet Loop 和 nextTick

## Scoped和样式 穿透



