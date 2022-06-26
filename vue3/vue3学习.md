# vue3学习

1. 只有响应式的类型在模板中才会表现为响应式；

2. vue2使用的是`defineProperty`的`set`,`get`方法实现的响应式，它有一些通病(参考官网：https://cn.vuejs.org/v2/guide/reactivity.html#%E5%AF%B9%E4%BA%8E%E5%AF%B9%E8%B1%A1)，比如对于对象，它无法检测到对象属性的添加和删除；对于对象不能检测以下数组的变动：

   * 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
   * 当你修改数组的长度时，例如：`vm.items.length = newLength`

   

   vue3使用了`Proxy`来实现响应式，解决了vue2对象和数组不能响应的问题：

   ```vue
   <template>
     <ul>
       <li v-for="num in numbers" :key="num">{{num}}</li>
     </ul>
     <div>{{person.name}}</div>
   </template>
   
   <script>
   import { reactive, toRefs } from 'vue'
   export default {
     name: "Test7",
     setup() {
       const data = reactive({
         numbers: [1, 2, 3, 4, 5],
         person: {
           name: 'tongwl'
         }
       })
       data.numbers.length = 3  //截取成功
       data.person.name = 'lily'  //修改成功
       console.log(toRefs(data))
       return {
         ...toRefs(data)
       }
     }
   }
   </script>
   
   <style scoped>
   
   </style>
   ```

   

## vue3的多种写法

* template写法
* tsx写法



#### 1. template写法

```vue
<template>
  <div>{{msg}}</div>
</template>

<script>
export default {
  name: "Test5",
  data() {
    return {
      msg: 'tongwl'
    }
  }
}
</script>

<style scoped>
</style>
```

如果需要ts的支持，可以给script标签添加上`lang="ts"`属性，例子：

```vue
<template>
  <div>{{msg}}</div>
</template>

<script lang="ts">
export default {
  name: "Test5",
  data() {
    return {
      msg: 'tongwl'
    }
  }
}
</script>

<style scoped>
</style>
```





##setup

* `ref`
* `computed`
* `reactive`
* `watch`

#### 1. ref用法

**ref的值一般是原始类型，如果值是对象，建议使用reactive**

```vue
<template>
  <div>{{count}}</div>
  <div><button @click="increase">Increase</button></div>
</template>

<script>
  import { ref } from 'vue'
  export default {
    name: "Test4",
    setup() {
      const count = ref(0)

      const increase = () => {
        count.value++
      }
      return {
        count,
        increase
      }
    }
  }
</script>
```



#### 2. computed用法

**computed返回的值也是ref类型**

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <div><button @click="increase">Increase</button></div>
</template>

<script>
import { ref, computed } from 'vue'
export default {
  name: "Test4",
  setup() {
    const count = ref(0)

    const double = computed(() => count.value * 2)
    console.log(double) // ComputedRefImp {..., value: 0}

    const increase = () => {
      count.value++
    }
    return {
      count,
      double,
      increase
    }
  }
}
</script>
```

**但是写在reactive中的computed，返回的并不是ref类型**

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <button @click="increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed, ref, toRef, toRefs } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2), //此时的double并不是Ref类型
      increase: () => {
        data.count++
      }
    })

    console.log(data.double) // 0, 它不是Ref类型，它是number类型

    return {
      count: toRef(data, 'count'),
      double: toRef(data, 'double'), //因为double不是ref类型，所以还是需要toRef去转换
      increase: toRef(data, 'increase')
    }
  }
}
</script>

<style scoped>
</style>
```



#### 3. reactive用法

正常例子：

```vue
<template>
  <div>{{data.count}}</div>
  <div>{{data.double}}</div>
  <button @click="data.increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => {
        data.count++
      }
    })
    return {
      data
    }
  }
}
</script>

<style scoped>
</style>
```

只有响应式的类型在模板中才会表现为响应式，以下的写法是不会有响应式的：

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <button @click="increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => {
        data.count++
      }
    })
    return {
      ...data //解构出来的数据不是响应式的
    }
  }
}
</script>

<style scoped>
</style>
```

可以通过`toRef`或则`toRefs`将非响应式数据转换为响应式数据：

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <button @click="increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed, toRefs } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => {
        data.count++
      }
    })
    return {
      ...toRefs(data) //解构出来的数据就是响应式的了
    }
  }
}
</script>

<style scoped>
</style>
```

```vue
<template>
  <div>{{count}}</div>
  <div>{{double}}</div>
  <button @click="increase">Increase</button>
</template>

<script lang="ts">
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
}
import { reactive, computed, toRef, toRefs } from 'vue'
export default {
  name: "Test5",
  setup() {
    const data: DataProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => {
        data.count++
      }
    })
    return {
      count: toRef(data, 'count'),
      double: toRef(data, 'double'),
      increase: toRef(data, 'increase')
    }
  }
}
</script>

<style scoped>
</style>
```



#### 4. watch

[watch官网参考](https://v3.cn.vuejs.org/guide/reactivity-computed-watchers.html#watch)

> 官网：A watch source can only be a getter/effect function, a ref, a reactive object, or an array of these types. 

**侦听器数据源可以是返回值的 getter / effect 函数，也可以直接是响应式类型，如 `ref`，`reactive`, 或则是数组值是这些类型的数组。**



##### ref的监听

```tsx
import { defineComponent } from 'vue'
import styles from './test.module.scss'
import { ref, computed, watch } from 'vue'
export default defineComponent({
  name: 'Test1',
  setup() {
    let count = ref(0)
    watch(count, (_new, _old) => { //监听的是ref事件
      console.log(_new)
      console.log(_old)
    })
    const increase = () => {
      count.value++
    }
    const double = computed(() => count.value * 2)
    return {
      count,
      double,
      increase
    }
  },
  render() {
    return <>
      <div>{this.count}</div>
      <div>{this.double}</div>
      <div><button onClick={this.increase}>Increase</button></div>
    </>
  }
})
```



#####reactive和getter函数的监听

```tsx
import { defineComponent } from 'vue'
import { reactive, computed, watch } from 'vue'
interface DtaProps {
  count: number;
  double: number;
  increase: () => void;
}
export default defineComponent({
  name: 'Test2',
  setup () {
    const data: DtaProps = reactive({
      count: 2,
      double: computed(() => data.count * 2),
      increase: () => { data.count++ }
    })

    // 正确写法
    watch(data, (_new) => { // 监测的是整个data
      console.log(_new)
    })

    // 错误写法
    watch(data.count as any, (_new) => { // data.count是一个number类型，并不能被正确的监测
      console.log(_new)
    })

    // 正确写法
    watch(() => data.count, _new => { // 这才是正确的监测reactive属性的写法
      console.log(_new)
    })

    return {
      data
    }
  },
  render() {
    return <>
      <div>{this.data.count}</div>
      <div>{this.data.double}</div>
      <div><button onClick={this.data.increase}>Increase</button></div>
    </>
  }
})
```



##### 监听多个数据源

**如果你在同一个函数里同时改变这些被侦听的来源，侦听器仍只会执行一次。**

```tsx
import { defineComponent } from 'vue'
import { reactive, computed, watch } from 'vue'
interface DtaProps {
  count: number;
  double: number;
  increase: () => void;
}
export default defineComponent({
  name: 'Test2',
  setup () {
    const data: DtaProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => { data.count++ }
    })

    // 监听多个数据源
    // 如果你在同一个函数里同时改变这些被侦听的来源，侦听器仍只会执行一次。
    watch([() => data.count, () => data.double], _new => {
      console.log(_new) //返回的数组 [1, 2], [2, 4]等
    })

    return {
      data
    }
  },
  render() {
    return <>
      <div>{this.data.count}</div>
      <div>{this.data.double}</div>
      <div><button onClick={this.data.increase}>Increase</button></div>
    </>
  }
})
```

通过更改设置 `flush: 'sync'`，我们可以为每个更改都强制触发侦听器，尽管这通常是不推荐的。

```tsx
import { defineComponent } from 'vue'
import { reactive, computed, watch } from 'vue'
interface DtaProps {
  count: number;
  double: number;
  increase: () => void;
}
export default defineComponent({
  name: 'Test2',
  setup () {
    const data: DtaProps = reactive({
      count: 0,
      double: computed(() => data.count * 2),
      increase: () => { data.count++ }
    })

    // 监听多个数据源
    // 由于设置了flush: 'sync', 一次increase返回分别会返回[1, 0] 和 [1, 2]
    watch([() => data.count, () => data.double], _new => {
      console.log(_new)
    }, {
      flush: 'sync'
    })

    return {
      data
    }
  },
  render() {
    return <>
      <div>{this.data.count}</div>
      <div>{this.data.double}</div>
      <div><button onClick={this.data.increase}>Increase</button></div>
    </>
  }
})
```

或者，可以用 [nextTick](https://v3.cn.vuejs.org/api/global-api.html#nexttick) 等待侦听器在下一步改变之前运行。例如：

```tsx
const changeValues = async () => {
  firstName.value = 'John' // 打印 ["John", ""] ["", ""]
  await nextTick()
  lastName.value = 'Smith' // 打印 ["John", "Smith"] ["John", ""]
}
```



#####侦听响应式对象

使用侦听器来比较一个数组或对象的值，这些值是响应式的，要求它有一个由值构成的副本。

```js
const numbers = reactive([1, 2, 3, 4])

watch(
  () => [...numbers],
  (numbers, prevNumbers) => {
    console.log(numbers, prevNumbers)
  }
)

numbers.push(5) // logs: [1,2,3,4,5] [1,2,3,4]
```

尝试检查深度嵌套对象或数组中的 property 变化时，仍然需要 `deep` 选项设置为 true。

```js
const state = reactive({ 
  id: 1,
  attributes: { 
    name: '',
  }
})

watch(
  () => state,
  (state, prevState) => {
    console.log('not deep', state.attributes.name, prevState.attributes.name)
  }
)

watch(
  () => state,
  (state, prevState) => {
    console.log('deep', state.attributes.name, prevState.attributes.name)
  },
  { deep: true }
)

state.attributes.name = 'Alex' // 日志: "deep" "Alex" "Alex"
```

然而，侦听一个响应式对象或数组将始终返回该对象的当前值和上一个状态值的引用。为了完全侦听深度嵌套的对象和数组，可能需要对值进行深拷贝。这可以通过诸如 [lodash.cloneDeep](https://lodash.com/docs/4.17.15#cloneDeep) 这样的实用工具来实现。

```js
import _ from 'lodash'

const state = reactive({
  id: 1,
  attributes: {
    name: '',
  }
})

watch(
  () => _.cloneDeep(state),
  (state, prevState) => {
    console.log(state.attributes.name, prevState.attributes.name)
  }
)

state.attributes.name = 'Alex' // 日志: "Alex" ""
```
