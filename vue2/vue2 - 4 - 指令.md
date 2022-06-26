# vue - 指令

vue中使用`v-`开头来表示Vue提供的特殊指令。



#### v-bind

属性绑定指令，例子：

```vue
<span v-bind:title="message">悬停内容</span>
```

`v-bind`也可以省略不写：

```vue
<span :title="message">悬停内容</span>
```



#### v-if, v-else-if, v-else





#### v-model

实现表单输入和应用状态的双向绑定，例子：

```vue
<span>{{ message }}</span>
<input type="text" v-model="message">
```



####v-once

通过使用`v-once`指令，执行的是**一次性地插值，也就是当数据改变时，插值处的内容不会更新**。

例子1：
`<div>{{message}}</div>`没有v-once，当点击Change按钮的时候，message会变化。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
  <div id="app">
    <div>{{message}}</div>
    <button @click="change">Change</button>
  </div>
  <script>
    const vm = new Vue({
      el: '#app',
      data: {
        message: 'tongwl'
      },
      methods: {
        change() {
          this.message = 'lily'
        },
      }
    })
  </script>
</body>
</html>
```

例子2：
`<div v-once>{{message}}</div>`存在v-once，当点击Change按钮的时候，message不会变化。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
  <div id="app">
    <div v-once>{{message}}</div>
    <button @click="change">Change</button>
  </div>
  <script>
    const vm = new Vue({
      el: '#app',
      data: {
        message: 'tongwl'
      },
      methods: {
        change() {
          this.message = 'lily'
        },
      }
    })
  </script>
</body>
</html>
```

