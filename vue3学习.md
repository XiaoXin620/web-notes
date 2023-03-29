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

## watch监听器

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























