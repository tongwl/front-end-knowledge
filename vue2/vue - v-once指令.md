# vue - v-once指令

通过使用`v-once`指令，执行的是一次性地插值，也就是当数据改变时，插值处的内容不会更新。

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

