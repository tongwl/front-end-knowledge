#Vue学习

vue的组件只允许一个根组件。。



1.组件数据绑定

```vue
<template>
  <div>
    {{message}}
  </div>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
        message: "I am Test",
      }
    }
  }
</script>
<style scoped></style>
```



2.组件属性绑定

```vue
<template>
  <div>
    <span v-bind:title="spanTitle">tongwl</span>
    <span :title="spanTitle">tongwl2</span>
  </div>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
        spanTitle: 'I am the title',
      }
    }
  }
</script>
<style scoped></style>
```



3.条件
`v-if` (元素是否删除)

```javascript
<template>
  <div>
    <span v-if="seen">tongwl</span>
    <span v-if="notSeen">lily</span>
  </div>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
        seen: true,
        notSeen: false,
      }
    }
  }
</script>
<style scoped></style>
```



`v-show` (元素是否隐藏：display:none;)

```vue
<template>
  <div>
    <div v-show="show">show</div>
    <div v-show="hidden">hide</div>
  </div>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
        show: true,
        hidden: false
      }
    }
  }
</script>
<style scoped></style>
```



4.循环

`v-for`

```vue
<template>
  <ul>
    <li v-for="todo in todos">
      {{todo}}
    </li>
  </ul>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
        todos: [1,2,3],
      }
    }
  }
</script>
<style scoped></style>
```



5.事件绑定
`v-on:click` 

```vue
<template>
  <button v-on:click="sayHello()">hello</button>
</template>
<script>
  export default {
    name: "Test",
    data() {
      return {
      }
    },
    methods: {
      sayHello() {
        alert('hello!');
      }
    }
  }
</script>
<style scoped></style>
```



6.v-model

表单输入和应用状态之间的双向绑定。

