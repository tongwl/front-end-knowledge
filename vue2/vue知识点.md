# vue知识点

1. 在vue组件中，<style scoped></style>，这个scoped attribute 会自动添加一个唯一的 attribute (比如 `data-v-21e5b78`) 为组件内 CSS 指定作用域，编译的时候 `.list-container:hover` 会被编译成类似 `.list-container[data-v-21e5b78]:hover`。

2. Vue 在不同组件间强制使用单向数据流。

3. 如何定义一个组件

   ```js
   Vue.component('todo-item', {
     props: ['todo'],
     template: '<li>{{ todo.text }}</li>'
   })
   
   var app7 = new Vue({
     el: '#app-7',  // option "el" can only be used during instance creation with the `new` keyword.
     data: {
       groceryList: [
         { id: 0, text: '蔬菜' },
         { id: 1, text: '奶酪' },
         { id: 2, text: '随便其它什么人吃的东西' }
       ]
     }
   })
   ```

4. 在组件中，即使最开始不需要使用某个值，但如果在后续中需要用到，那么就应该在data中将这个设置一个默认值，因为组件的data属性只有在组件实例创建的时候才是响应式的。

5. 在vue中存在一些全局变量，我们可以在`{{}}`中使用他们，以下是全局变量白名单：

   ```js
   const allowedGlobals = makeMap(
       'Infinity,undefined,NaN,isFinite,isNaN,' +
       'parseFloat,parseInt,decodeURI,decodeURIComponent,encodeURI,encodeURIComponent,' +
       'Math,Number,Date,Array,Object,Boolean,String,RegExp,Map,Set,JSON,Intl,' +
       'require' // for Webpack/Browserify
     )
   ```

6. v-html的内容并不属于vue模板中，<style scoped></style>的样式并不会作用到v-html，想要解决这个问题，可以使用css modules，或则定义一个全局<style></style>来解决。

7. `this.$data`可以获得所有data绑定的值。(经过测试，不包含computed的值)

8. >  注意：为了区分自定义的属性和Vue内置属性，以`_`或`$`开头的属性并不会被Vue实例代理，也就是说在data中定义了一个\$username，这个\$username不能被this.\$username读取，也不能赋值，但可以使用this.\$data.\$username的方式访问这些property。

   > 如果需要，可以通过将 `vm.$data` 传入 `JSON.parse(JSON.stringify(...))` 得到深拷贝的原始数据对象。

9. **`{{}}`双大括号会将数据解释为普通文本**，而非 HTML 代码。为了输出真正的 HTML，你需要使用`v-html` 指令。

10. ```<button v-bind:disabled="isButtonDisabled">button</button>```

   如果 `isButtonDisabled` 的值是 `null`、`undefined` 或 `false`，则 `disabled`不生效，其他都生效。
   所以当isButtonDisabled是0，或则`''`空字符串的时候，disabled还是会起作用。

   但是对于`v-if`和`v-show`，它的值会被转换成布尔类型后再判断，所以

   ```html
   <div v-if="0">aaaa</div>  <!-- 元素会被删除 -->
   <div v-if="''">aaaa</div>  <!-- 元素会被删除 -->
   <div v-if="false">aaaa</div>  <!-- 元素会被删除 -->
   <div v-if="undefined">aaaa</div>  <!-- 元素会被删除 -->
   <div v-if="null">aaaa</div>  <!-- 元素会被删除 -->
   ```

   

9. Next