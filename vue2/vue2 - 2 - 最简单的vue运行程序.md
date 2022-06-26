# vue2 - 最简单的vue运行程序

例子1：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
  <div id="app">{{message}}</div>
  <script>
    const vm = new Vue({
      el: '#app',
      data: {
        message: 'tongwl'
      }
    })
  </script>
</body>
</html>
```



例子2：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
  <div id="app">{{message}}</div>
  <script>
    // 下面的实例并未使用el挂载dom元素
    const vm = new Vue({
      data: {
        message: 'tongwl'
      }
    })
    vm.$mount('#app') //使用$mount手动挂载dom元素
  </script>
</body>
</html>
```

