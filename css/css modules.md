# css modules

参考文档：
[浅谈CSS Modules以及CSS Modules在Vue.js上的使用][https://blog.csdn.net/weixin_44869002/article/details/105806021]

[阮一峰CSS Modules 用法教程][http://www.ruanyifeng.com/blog/2016/06/css_modules.html]



本文将从以下3点讲解css modules。

* 什么是`css modules`
* `css modules`诞生的原因
* 如何使用`css modules`



## 什么是`css modules`

- **CSS Modules**将 CSS 的 class 和动画名称做了限定，让全局生效的CSS样式默认只在本地（当前）作用域生效。
- **CSS Modules**并不是新增的官方规范或者浏览器新增机制，它只是一个解决某一类问题的解决方案，需要借助构建工具（如**Webpack**）去实现。



## 为什么会有`css modules`的出现

####样式全局化
导致CSS Modules诞生的原因之一是CSS生效机制——样式全局化。稍微回顾之前的开发我们就会发现，我们写的CSS的作用域都是整个网页。但又一想，可能就有人觉得：”这不是很合理嘛！我写给这个页面的CSS在这个页面生效不很正常嘛！它又不会跑到其他页面生效，为什么会说全局化呢？“

确实，相对于非框架（框架：Vue.js、Angular等）搭建页面来说，表面上看并没有全局化。但是需要注意的是，现在的前端开发基本上都是基于Vue.js、Angular和React.js三大框架进行开发的。他们之间有一个共同的特点就是组件化(模块化)。也就是说，现在的网页开发不再是在单纯的HTML，JS或则css文件上开发了。基于框架的开发是在许多的框架组件上进行开发的。就拿Vue.js来说，Vue.js是一个单页面系统，它的页面跳转操作基本都是通过切换组件来完成的。换而言之，Vue.js操作的页面自始至终都是同一个HTML文件。

 因此，组件内部的CSS最终在网页生效时都会被提升到页面全局。这样就会导致一个问题——如果当前页面有两个组件(A组件和B组件)，它们都有同样的class。那么最终在页面渲染时必定造成样式覆盖。这就是模块化开发导致的CSS问题。CSS Modules的诞生就是解决这一问题的方案之一。

如果我们了解**模块化**，就会知道**模块化**有一个特点——**相对独立、互不干扰**。这一特点正好也是解决**样式全局化**的思路。



##`css modules`是怎么解决问题的

其实呢！样式全局化问题本质就是因为样式选择器重名而导致样式覆盖的问题。就算没有模块化，我们在开发中也会经常遇到。解决方法我们也都非常清楚。无非就以下几种：

* 替每个样式都起与众不同的名字，保证不会出现重名就行；(css modules正是采用了这种方法)

* 增加CSS样式选择器的深度，提高样式的权重；

* 使用`!important`提高所要生效样式的权重；

* 替当前模块设置一个nameSpace和其他模块进行区分；

  上面四种方案中，方案二和方案三仅仅是强制让自己所希望的样式生效，本质上还是对样式覆盖了。而方案一和方案四是比较好的解决方法。这两个方法都是从根本上避免了样式覆盖的可能性。CSS Modules就是采用了第一种方法。CSS Modules在最终构建页面时会自动重命名class，保证全部的class都不会重名。

例如：下面是Vue里一个组件，其样式使用了CSS Modules

```html
<template>
  <div class="hello">
    <!-- 使用 CSS Modules 声明的样式 -->
    <h1 :class="$style.color">{{ msg }}</h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      msg: 'test'
    }
  }
}
</script>

// 使用 CSS Modules
<style module>
.color {
  color: #42b983;
}
</style>
```

编辑出来的结果是h1的class会变成.HelloWorld_color_1DT2e，也就是

```html
<h1 class="HelloWorld_color_1DT2e">test</h1>
```

.HelloWorld_color_1DT2e就是一个唯一独立的class名。



## 如何使用`css modules`

关于**CSS Modules**的使用在三大框架里大同小异。下面我将讲解的在**Vue.js**里面**CSS Modules**的使用方法。

####浅谈CSS Modules 在Vue.js 的使用

前面的官方文档讲过，**CSS Modules**需要借助构建工具来使用。那么，[**Vue CLI**](https://cli.vuejs.org/zh/)的构建工具是**Webpack**。所以，想要在项目中使用**CSS Modules**就必须先配置`webpack.config.js`。~~配置如下：~~

```js
{
  module: {
    rules: [
      // ... 其它规则省略
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          {
            loader: 'css-loader',
            options: {
              // 开启 CSS Modules
              modules: true
            }
          }
        ]
      }
    ]
  }
}
```



**上述是老的Vue CLI，现在的Vue CLI已经不需要任何额外配置([官网说明][https://cli.vuejs.org/zh/guide/css.html#css-modules])，可以通过 `<style module>` 以开箱即用的方式[在 `*.vue` 文件中使用 CSS Modules](https://vue-loader.vuejs.org/zh/guide/css-modules.html) **



配置完后，我们就可以在组件内使用**CSS Modules**了。使用方法如下：

```vue
/* 在 <style> 上添加 module 特性 */
<style module>
.color {
  color: #42b983;
}
</style>
```

在模板中使用**CSS Modules**声明的class:

```html
<template>
  <div class="hello">
    <!-- 使用 CSS Modules 声明的样式 -->
    <h1 :class="$style.color">{{ msg }}</h1>
    {{base}}
  </div>
</template>
```

上面就是**CSS Modules**的基本使用方法了。除了上面讲到了，**CSS Modules**还设计一些基本语法：

1. :local() 与 :global()
   **CSS Modules**分**局部作用域**和**全局作用域**。两者的区分是通过`:local()` 与`:global()`来设定的。因为**CSS Modules**默认的是**局部作用域**，所以，`:local()`默认省略。因此，上面例子的CSS部分也可以这样写：

   ```html
   <style module>
   :local(.color) {
     color: #42b983;
   }
   </style>
   ```

   如果要某个样式暴露给全局则需要使用`:global()`：

   ```html
   <style module>
   /* 使用 CSS Modules */
   .color {
     color: #42b983;
   }
   :global(.g_color){
     color: #14d5ee;
   }
   </style>
   ```

   需要注意的是，`:global()`修饰的样式是**不会被重命名的**，使用全局样式时直接赋值给class就行了，不需要进行类绑定。

   ```html
   <template>
     <div class="hello">
       <!-- 使用 CSS Modules 声明的样式 -->
       <h1 :class="$style.color">{{ msg }}</h1>
       <h2 class="g_color">使用:global 声明的样式</h2>
     </div>
   </template>
   
   <script>
   export default {
     name: 'HelloWorld',
     props: {
       msg: String
     }
   }
   </script>
   
   <style module>
   /* 使用 CSS Modules */
   .color {
     color: #42b983;
   }
   :global(.g_color){
     color: #14d5ee;
   }
   </style>
   ```

   

2. `composes`：class的组合
   在**CSS Modules**可以将两个选择器的样式组合在一起。换而言之，一个选择器可以**继承**另一个选择的样式。通过`composes`来实现。`composes`两者来源的class:

   ```css
   .bg_green{
     background-color: green;
   }
   
   .text_One{
     composes: bg_green;
     color: aqua;
   }
   ```

   也可以导入样式表的class:
   test.module.css
   
   ```css
   /*
   	注意:
   	如果命名是bg-color，并不会被识别
   */
   .bgColor {
       background: blue;
   }
   ```
   
   Test.vue
   
   ```vue
   <template>
     <div :class="$style.red" v-html="msg"></div>
   </template>
   
   <script>
     export default {
       name: "Test",
       data() {
         return {
           msg: 'tongwl'
         }
       }
     }
   </script>
   
   <style module>
     .red {
       composes: bgColor from './test.module.css';
       color: red;
     }
   </style>
   ```
   
   