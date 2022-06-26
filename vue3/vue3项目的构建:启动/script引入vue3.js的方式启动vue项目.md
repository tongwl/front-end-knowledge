# <script>引入vue3.js的方式启动vue项目

(以下源码可以直接运行使用)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue Mastery</title>
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@3.0.0-beta.12/dist/vue.global.js"></script>
  </head>
  <body>
    <div id="app">
      <h1>{{username}}</h1>
    </div>
    <script>
      //创建一个vue app
      const app = Vue.createApp({
        data() {
          return {
            username: 'tongwl',
          }
        }
      });

      //将vue app绑定到#app元素上，也就是vue的语法仅仅作用于#app内
      const mountedApp = app.mount('#app');
    </script>
  </body>
</html>
```

