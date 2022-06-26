# emits

[官网参考](https://v3.cn.vuejs.org/guide/migration/emits-option.html#%E6%A6%82%E8%BF%B0)

[官网参考2](https://v3.cn.vuejs.org/api/options-data.html#emits)



> 强烈建议使用 `emits` 记录每个组件所触发的所有事件。
>
> 这尤为重要，因为我们[移除了 `.native` 修饰符](https://v3.cn.vuejs.org/guide/migration/v-on-native-modifier-removed.html)。任何未在 `emits` 中声明的事件监听器都会被算入组件的 `$attrs`，并将默认绑定到组件的根节点上。



## 概述

Vue 3 现在提供一个 `emits` 选项，和现有的 `props` 选项类似。这个选项可以用来定义一个组件可以向其父组件触发的事件。



## 2.x 的行为

在 Vue 2 中，你可以定义一个组件可接收的 prop，但是你无法声明它可以触发哪些事件：

```vue
<template>
  <div>
    <p>{{ text }}</p>
    <button v-on:click="$emit('accepted')">OK</button>
  </div>
</template>
<script>
  export default {
    props: ['text']
  }
</script>
```



## 3.x 的行为

和 prop 类似，现在可以通过 `emits` 选项来定义组件可触发的事件：

```vue
<template>
  <div>
    <p>{{ text }}</p>
    <button v-on:click="$emit('accepted')">OK</button>
  </div>
</template>
<script>
  export default {
    props: ['text'],
    emits: ['accepted']
  }
</script>
```

该选项也可以接收一个对象，该对象允许开发者定义传入事件参数的验证器，和 `props` 定义里的验证器类似。



## 迁移策略

**强烈建议使用 `emits` 记录每个组件所触发的所有事件。**

**这尤为重要，因为我们[移除了 `.native` 修饰符](https://v3.cn.vuejs.org/guide/migration/v-on-native-modifier-removed.html)。任何未在 `emits` 中声明的事件监听器都会被算入组件的 `$attrs`，并将默认绑定到组件的根节点上。**

> 经过测试，上述特性只会在.vue中出现，使用tsx并不会出现这种现象。但是为了统一，还是建议使用emits。

### 示例

对于向其父组件透传原生事件的组件来说，这会导致有两个事件被触发：

```vue
<template>
  <button v-on:click="$emit('click', $event)">OK</button>
</template>
<script>
export default {
  emits: [] // 不声明事件
}
</script>
```

当一个父级组件拥有 `click` 事件的监听器时：

```html
<my-button v-on:click="handleClick"></my-button>
```

该事件现在会被触发*两次*:

- 一次来自 `$emit()`。
- 另一次来自应用在根元素上的原生事件监听器。

现在你有两个选项：

1. 正确地声明 `click` 事件。当你真的在 `<my-button>` 的事件处理器上加入了一些逻辑时，这会很有用。
2. 移除透传的事件，因为现在父组件可以很容易地监听原生事件，而不需要添加 `.native`。适用于你只想透传这个事件。



## 实际案例

### .vue组件案例

Father.vue

```vue
<template>
  <div>
    <button @click="openModel">Open model</button>
    <Child :modelVisible="modelVisible" @closeModel="onCloseModel"></Child>
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue'
import Child from "./Child.vue";
export default defineComponent({
  name: "Father",
  components: { Child },
  setup() {
    const modelVisible = ref(false)
    const openModel = () => {
      modelVisible.value = true
    }

    const onCloseModel = () => {
      modelVisible.value = false
    }

    return {
      modelVisible,
      openModel,
      onCloseModel
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
  <div v-if="modelVisible">
    <header>I am model</header>
    <button @click="closeModel">Close model</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "Child",
  props: {
    modelVisible: Boolean,
  },
  emits: ['closeModel'],
  setup(props, cxt) {
    const closeModel = () => {
      cxt.emit('closeModel')
    }
    return {
      closeModel
    }
  }

})
</script>

<style scoped>

</style>
```



### tsx组件案例

注意：

* **如果我们在Child.tsx定义了一个emit叫做`closeModel`，那么我们在Father.tsx中传入的参数需要带上`on`，就是`onCloseModel = {this.onCloseModel}`;**
* **如果我们在Child.tsx定义了一个emit叫做`close-model`，那么我们在Father.tsx中传入的参数需要带上`on`，就是`onClose-model = {this.onCloseModel}`;**

Father.tsx

```tsx
import { defineComponent, ref } from "vue";
import Child from "./Child";
export default defineComponent({
  name: 'Father',
  components: { Child },
  setup() {
    const modelVisible = ref(false)

    const openModel = () => {
      modelVisible.value = true
    }

    const onCloseModel = () => {
      modelVisible.value = false
    }

    return {
      modelVisible,
      openModel,
      onCloseModel
    }
  },
  render() {
    return <div>
      <button onClick={this.openModel}>Open model</button>
      <Child modelVisible={this.modelVisible} onCloseModel={this.onCloseModel}></Child>
    </div>
  }
})
```



Child.tsx

```tsx
import { defineComponent, watch } from "vue";

export default defineComponent({
  name: 'Father',
  emits: ['closeModel'],
  props: {
    modelVisible: Boolean,
  },
  setup(props, context) {
    const closeModel = () => {
      context.emit('closeModel')
    }

    return {
      closeModel
    }
  },
  render() {
    return <>
      {
        this.modelVisible && <div>
          <div>I am model</div>
          <button onClick={this.closeModel}>Close</button>
        </div>
      }
    </>
  }
})
```



### emits属性说明

- **类型：**`Array<string> | Object`

- **详细：**

  emits 可以是数组或对象，从组件触发自定义事件，emits 可以是简单的数组，也可以是对象，后者允许配置事件验证。

  在对象语法中，每个 property 的值可以为 `null` 或验证函数。验证函数将接收传递给 `$emit` 调用的其他参数。如果 `this.$emit('foo',1)` 被调用，`foo` 的相应验证函数将接收参数 `1`。验证函数应返回布尔值，以表示事件参数是否有效。

- **用法：**

  ```js
  const app = createApp({})
  
  // 数组语法
  app.component('todo-item', {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  })
  
  // 对象语法
  app.component('reply-form', {
    emits: {
      // 没有验证函数
      click: null,
  
      // 带有验证函数
      submit: payload => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Invalid submit event payload!`)
          return false
        }
      }
    }
  })
  ```

  

  **与组件和 prop 一样，事件名提供了自动的大小写转换。如果在子组件中触发一个以 camelCase (驼峰式命名) 命名的事件，你将可以在父组件中添加一个 kebab-case (短横线分隔命名) 的监听器。**

  ```js
  this.$emit('myEvent')
  ```

  ```html
  <my-component @my-event="doSomething"></my-component>
  ```

  与 [props 的命名](https://v3.cn.vuejs.org/guide/component-props.html#prop-的大小写命名-camelcase-vs-kebab-case)一样，当你使用 DOM 模板时，我们建议使用 kebab-case 事件监听器。如果你使用的是字符串模板，这个限制就不适用。

  

  注意

  >  `emits` 选项中列出的事件**不会**从组件的根元素继承，也将从 `$attrs` property 中移除。



### 用.vue写法如果不加emits的副作用

Father.vue

```vue
<template>
  <div>
    <button @click="openDialog">Open dialog</button>
    <Child :dialogVisible="dialogVisible" @click="onCloseDialog"></Child>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import Child from './Child1.vue'


export default defineComponent({
  name: 'Father1',
  components: { Child },
  setup() {
    const dialogVisible = ref(false)

    const openDialog = () => {
      dialogVisible.value = true
    }

    const onCloseDialog = () => {
      console.log('被调用')  //当在Child.vue中点击Close dialog的时候，此处会被调用两次
      dialogVisible.value = false
    }

    return {
      dialogVisible,
      openDialog,
      onCloseDialog
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
  <div v-if="dialogVisible">
    <div>I am dialogaaa</div>
    <button @click="closeDialog">Close dialog</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: "Child1",
  props: {
    dialogVisible: Boolean
  },
  // emits: ['click'],
  setup(props, context) {
    const closeDialog = () => {
        context.emit('click')
    }

    return {
      closeDialog
    }
  }
})
</script>

<style scoped>

</style>
```



###用.tsx写法如果不加emits，却不会有副作用

> 虽然.tsx不会有.vue的调用多次的副作用，但是会提示属性不存的ts错误，所以强烈建议使用emits

Father.tsx

```tsx
import { defineComponent, ref } from 'vue'
import Child1 from './Child1'

export default defineComponent({
  name: 'Father',
  components: {Child1},
  setup() {
    const modelVisible = ref(false)

    const openModel = () => {
      modelVisible.value = true
    }

    const onCloseModel = () => {
      console.log('被调用') //当在Child.vue中点击Close dialog的时候，此处只会调用两次
      modelVisible.value = false
    }

    return {
      modelVisible,
      openModel,
      onCloseModel
    }
  },
  render() {
    return <div>
      <button onClick={this.openModel}>Open model</button>
      <Child1 onClick={this.onCloseModel} modelVisible={this.modelVisible}></Child1>  {/*此处提示onClick报错，但是不影响执行*/}
    </div>
  }
})
```



Child.tsx

```tsx
import { defineComponent, watch } from "vue";

export default defineComponent({
  name: 'Child1',
  props: {
    modelVisible: Boolean
  },
  // emits: ['click'],
  setup(props, cxt) {
    const closeModel = () => {
      cxt.emit('click')
    }

    watch(() => props.modelVisible, (_new, _old) => {
      console.log(_new)
      console.log(_old)
    })

    return {
      closeModel
    }
  },
  render() {
    return <>
      {
        this.modelVisible && <><div>I am model bbb</div>
        <button onClick={this.closeModel}>Close model</button>
        </>
      }
      </>
  }
})
```



