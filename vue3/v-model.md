#v-model

**使用v-model可以解决自定义组件和父组件之间的双向数据绑定，也就是数据在子组件更新，也能更新到父组件。**



[参考文章1](https://zhuanlan.zhihu.com/p/342617376)

[参考官网](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-%E5%8F%82%E6%95%B0)

## v-model作用于input元素

例子：

```tsx
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: 'Test1',
  setup() {
    const txt = ref<string | number>('william')
    const changeText = () => {
      txt.value = Math.random()
    }
    return {
      txt,
      changeText
    }
  },
  render() {
    return <div>
      <input type="text" v-model={this.txt}/>
      <div><button onClick={this.changeText}>Change text</button></div>
    </div>
  }
})
```



## v-model作用于自定义组件

### 基础使用

默认情况下，组件上的 `v-model` 使用 `modelValue` 作为 prop 和 `update:modelValue` 作为事件。我们可以通过向 `v-model` 传递参数来修改这些名称：

```js
<ChildComponent v-model = "title">
```

它是下面这种写法的简写：

```js
<ChildComponent :modelValue = "title" @update:modelValue = "title = $event">
```

####tsx例子：

例子1：

Father.tsx

```tsx
import { defineComponent, ref, watch } from "vue";
import Child2 from "./child2";
export default defineComponent({
  name: 'Father',
  components: {
    Child2
  },
  setup() {
    const title = ref('lily')
    watch(title, _new => {
      console.log('======当Child中的modelValue属性变化的时候，title也会变化======')
      console.log(_new)
    })
    return {
      title
    }
  },
  render() {
    return <div>
      <Child2 v-model={this.title}></Child2>
    </div>
  }
})
```

Child.tsx

```tsx
import { defineComponent, ref, h } from "vue";
export default defineComponent({
  name: 'child2',
  props: {
    modelValue: String
  },
  emits: ['update:modelValue'],
  setup() {

  },
  render() {
    /*
    return h('input', {
      type: 'text',
      value: this.modelValue,
      onInput: ($event: any) => {this.$emit('update:modelValue', $event.target.value)}
    })
    */
    return <input type="text" value={this.modelValue} onInput={($event: any) => this.$emit('update:modelValue',$event.target.value)}/>
  }
})
```



上述例子等同于：

Father2.tsx

```tsx
import { defineComponent, ref, watch } from 'vue'
import Child3 from './Child3'
export default defineComponent({
  name: 'Father2',
  setup() {
    const title = ref('lily')

    watch(title, _new => {
      console.log('======当Child中的modelValue属性变化的时候，title也会变化======')
      console.log(_new)
    })

    return {
      title
    }
  },
  render() {
    return <Child3 modelValue={this.title} onUpdate:modelValue={value => this.title = value}></Child3>
  }
})
```

Child2.tsx

```tsx
import { defineComponent } from "vue";
export default defineComponent({
  name: 'Child2',
  emits: {
    'update:modelValue': null
  },
  props: {
    modelValue: String
  },
  setup() {

  },
  render() {
    return <input type="text" value={this.modelValue} onInput={($event:any) => { this.$emit('update:modelValue', $event.target.value) }}/>
  }
})
```



####vue组件例子

例子1：

Father.vue

```vue
<template>
  <Child4 v-model="username"></Child4>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue'
import Child4 from './Child4.vue'
export default defineComponent({
  name: "Father",
  components: {
    Child4
  },
  setup() {
    const username = ref('william tong')

    watch(username, _new => {
      console.log('======当Child中的modelValue属性变化的时候，username也会变化======')
      console.log(_new)
    })

    return {
      username
    }
  }
})
</script>

<style scoped>

</style>
```

Child.vue

```vue
<template>
  <input type="text" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
</template>

<script>
import { defineComponent } from "vue";
export default defineComponent({
  name: 'Child4',
  emits: {
    'update:modelValue': null
  },
  props: {
    modelValue: String
  },
  setup() {

  }
})
</script>

<style scoped>

</style>
```



上述例子等同于：

Father.vue

```vue
<template>
  <Child5 :model-value="username" @update:modelValue="username = $event"></Child5>
</template>

<script>
import { defineComponent, ref, watch } from 'vue'
import Child5 from './Child5.vue'
export default defineComponent({
  name: 'Father5',
  components: { Child5 },
  setup() {
    const username = ref('lily song')

    watch(username, _new => {
      console.log('======当Child中的modelValue属性变化的时候，username也会变化======')
      console.log(_new)
    })

    return {
      username
    }
  }
})
</script>

<style scoped>

</style>
```

Child.vue

```vue
<template>
  <input type="text" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'Child5',
  props: {
    modelValue: String
  },
  emits: ['update:modelValue']
})
</script>

<style scoped>

</style>
```



###`v-model` 带参数

默认情况下，组件上的 `v-model` 使用 `modelValue` 作为 prop 和 `update:modelValue` 作为事件。我们可以通过向 `v-model` 传递参数来修改这些名称：

vue写法：

```html
<my-component v-model:title="bookTitle"></my-component>
```

tsx写法：

```tsx
//写法1
<my-component v-model:title={this.bookTitle}></my-component>

//写法2
<my-component v-model={[this.bookTitle, 'title']}></my-component>
```





在本例中，子组件将需要一个 `title` prop 并发出 `update:title` 事件来进行同步：

```js
app.component('my-component', {
  props: {
    title: String
  },
  emits: ['update:title'],
  template: `
    <input
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)">
  `
})
```

例子1：

Father.tsx

```tsx
import { defineComponent, ref, watch } from 'vue'
import Child6 from './Child6'
export default defineComponent({
  name: 'Test6',
  components: {
    Child6
  },
  setup() {
    let title = ref('william tong2')

    watch(title, _new => {
      console.log('======当Child中的modelValue属性变化的时候，username也会变化======')
      console.log(_new)
    })

    return {
      title
    }
  },
  render() {
    return <Child6 v-model:title={this.title}></Child6>
  }
})
```

Child.tsx

```tsx
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'Child6',
  props: {
    title: String
  },
  emits: ['update:title'],
  render() {
    return <input type="text" value={this.title} onInput={($event:any) => this.$emit('update:title', $event.target.value)}/>
  }
})
```



例子2：

Father.tsx

```tsx
import { defineComponent, ref, watch } from 'vue'
import Child7 from './Child7'

export default defineComponent({
  name: 'Test7',
  components: {
    Child7
  },
  setup() {
    const childCount = ref(0)

    watch(childCount, _new => {
      console.log('======当Child中的count属性变化的时候，childCount也会变化======')
      console.log(_new)
    })

    return {
      childCount
    }
  },
  render() {
    return <Child7 v-model={[this.childCount, 'count']}></Child7>
  }
})
```

Child.tsx

```tsx
import { defineComponent, watch } from 'vue'
export default defineComponent({
  name: 'Child7',
  props: {
    count: Number
  },
  emits: {
    'update:count': (payload: number) => {
      if (payload === 10) {
        return false
      }
      return true
    }
  },
  // props是响应式的
  setup(props: any, context) {
    watch(() => props.count, _new => {
      console.log('===count change===')
      console.log(_new)
    })

    const addCount = () => {
      context.emit('update:count', props.count + 1)
    }

    return {
      addCount
    }
  },
  render() {
    return <div>
        <div>count is {this.count}</div>
        <button onClick={this.addCount}>Add</button>
      </div>
  }
})
```



###多个 `v-model` 绑定

正如我们之前在 [`v-model` 参数](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-参数)中所学的那样，通过利用以特定 prop 和事件为目标的能力，我们现在可以在单个组件实例上创建多个 v-model 绑定。

每个 v-model 将同步到不同的 prop，而不需要在组件中添加额外的选项：

```html
<user-name
  v-model:first-name="firstName"
  v-model:last-name="lastName"
></user-name>
```

```js
app.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName'],
  template: `
    <input 
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
})
```



