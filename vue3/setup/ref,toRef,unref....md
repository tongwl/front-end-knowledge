# ref

* `ref` - 接受一个内部值并返回一个响应式且可变的 ref 对象
* `unref` - 如果参数是一个`ref`，则返回内部值，否则返回参数本身。
* `toRef` - 可以用来为源响应式对象上的某个 property 新创建一个 `ref`。
* `toRefs` - 将响应式对象(一般指reactive对象)转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 `ref`。
* `isRef` - 检查值是否为一个ref对象。 
* `customRef` - 创建一个自定义ref



接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象仅有一个 `.value` property，指向该内部值。

**示例：**

```js
const count = ref(0)
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

如果将对象分配为 ref 值，则它将被 [reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 函数处理为深层的响应式对象。

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

有时我们可能需要为 ref 的内部值指定复杂类型。可以在调用 `ref` 时传递一个泛型参数以覆盖默认推断，从而简洁地做到这一点：

```ts
const foo = ref<string | number>('foo') // foo 的类型：Ref<string | number>

foo.value = 123 // ok!
```



## `unref`

如果参数是一个 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)，则返回内部值，否则返回参数本身。这是 `val = isRef(val) ? val.value : val` 的语法糖函数。

```ts
function useFoo(x: number | Ref<number>) {
  const unwrapped = unref(x) // unwrapped 现在一定是数字类型
}
```

例子：

```tsx
import {defineComponent, ref, unref} from "vue";

export default defineComponent({
  name: "Test3",
  setup() {
    const count = ref(1)
    const count2 = 3
    console.log(unref(count)) // 输出1
    console.log(unref(count2)) // 输出3
  }
})
```



## `toRef`

可以用来为源响应式对象上的某个 property 新创建一个 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。然后，ref 可以被传递，它会保持对其源 property 的响应式连接。

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

fooRef.value++
console.log(state.foo) // 2

state.foo++
console.log(fooRef.value) // 3
```

当你要将 prop 的 ref 传递给复合函数时，`toRef` 很有用：

```js
export default {
  setup(props) {
    useSomeFeature(toRef(props, 'foo'))
  }
}
```

即使源 property 不存在，`toRef` 也会返回一个可用的 ref。这使得它在使用可选 prop 时特别有用，可选 prop 并不会被 [`toRefs`](https://v3.cn.vuejs.org/api/refs-api.html#torefs) 处理。





## `toRefs`

将响应式对象(一般指reactive对象)转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。

[toRefs官网参考](https://v3.cn.vuejs.org/api/refs-api.html#torefs)

以下是错误例子：

```tsx
import {defineComponent, reactive} from "vue";

export default defineComponent({
  name: 'Test4',
  setup() {
    const data = reactive({
      count: 0,
      increase: () => {
        data.count++
      }
    })

    return {
      count: data.count,  // 此时的count并不是响应式类型，它只是一个number类型
      increase: data.increase,// 此时的increase并不是响应式类型的方法，它只是一个普通方法
    }
  },
  render() {
    return <>
      <div>{this.count}</div>
      <div><button onClick={this.increase}>Increase</button></div>  { /* 点击并不会有响应式行为 */ }
    </>
  }
})
```

所以我们可以使用`toRefs`将响应式对象(一般指reactive对象)转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。

正确例子：

```tsx
import { defineComponent, reactive, toRefs } from "vue";

export default defineComponent({
  name: 'Test4',
  setup() {
    const data = reactive({
      count: 0,
      increase: () => {
        data.count++
      }
    })

    return {
      ...toRefs(data)
    }
  },
  render() {
    return <>
      <div>{this.count}</div>
      <div><button onClick={this.increase}>Increase</button></div>  { /* 点击就会有响应式行为 */ }
    </>
  }
})
```



## `customRef`

创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。它需要一个工厂函数，该函数接收 `track` 和 `trigger` 函数作为参数，并且应该返回一个带有 `get` 和 `set` 的对象。

使用自定义 ref 通过 `v-model` 实现 防抖节流 的示例：

