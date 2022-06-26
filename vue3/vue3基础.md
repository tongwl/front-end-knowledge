#vue3 基础



#### 简单启用一个vue3项目的例子:

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



#### {{}}

```html
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
```



#### v-bind:

属性绑定，也可以省略v-bind直接使用`:`简写。



#### v-show



#### v-if

####v-else-if

#### v-else



####v-for



#### v-on:click="onClickEvent"

####@click="onClickEvent('123')"





####:class

用法1

```html
<div :class="className">content</div>
```

用法2

对象的属性的key表示类名，属性值表示是否给这个元素赋予此class。

```html
<button :class="{disabledButton: !inStock}">Add to Cart</button>
```

用法3

inStock是判断条件，`enable-button`和`disable-button`是类名。

```html
<button :class="[inStock ? 'enable-button' : 'disable-button']">aaaa</button>
```



#### :style

style对象的属性可以使用css原生属性名，也可以使用驼峰命名法。

例子1:

```html
<div id="app">
	<div :style="{backgroundColor: color, 'font-size': size}">background is red.</div>
</div>

<script>
const app = Vue.createApp({
  data() {
    return {
      color: '#f00',
      size: '12px'
    }
  }
});
</script>
```

例子2:

```html
<div id="app">
	<div :style="style">content.</div>
</div>

<script>
const app = Vue.createApp({
  data() {
    return {
      style: {
                padding: '10px',
                fontWeight: 'bold',
                fontSize: '20px',
                color: '#0f0',
            },
    }
  }
});
</script>
```



#### computed

```html
<script>
const app = Vue.createApp({
  data() {
    return {
      content: '111',
    }
  },
  computed: {
  	result() {
      return this.content + '  haha',
    }
  }
});
</script>
```

