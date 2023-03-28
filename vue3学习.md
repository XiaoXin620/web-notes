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

































