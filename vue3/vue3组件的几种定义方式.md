# vue3组件的几种定义方式

* tsx
* `.vue`组件





## tsx

```tsx
import { defineComponent } from 'vue'
import styles from './test.module.scss'
export default defineComponent({
  name: 'Test1',
  setup() {
    return {
      msg: 'aaa'
    }
  },
  render() {
    return <div class={styles.test}>{this.msg}</div>
  }
})
```





##`.vue`组件

基础写法：

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

如果需要ts的支持，可以给script标签添加上`lang="ts"`属性，例子：(不推荐)

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

为了更好的支持ts，我们可以使用defineComponent来定义一个组件，使用defineComponent之后，会得到idea工具的type提示，这会更加方便和友好。例子：(**推荐使用defineComponent这种ts写法**)

```vue
<template>
  <div>{{ message }}</div>
</template>

<script>
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'Test1',
  data() {
    return {
      message: 'I am tongwl'
    }
  }
})
</script>

<style scoped>

</style>
```

