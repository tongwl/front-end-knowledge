# vue - v-html

- **预期**：`string`

- **详细**：

  更新元素的 `innerHTML`。**注意：内容按普通 HTML 插入 - 不会作为 Vue 模板进行编译**。如果试图使用 `v-html` 组合模板，可以重新考虑是否通过使用组件来替代。

  在网站上动态渲染任意 HTML 是非常危险的，因为容易导致 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。只在可信内容上使用 `v-html`，**永不**用在用户提交的内容上。

  在[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)里，`scoped` 的样式不会应用在 `v-html` 内部，因为那部分 HTML 没有被 Vue 的模板编译器处理。如果你希望针对 `v-html` 的内容设置带作用域的 CSS，你可以替换为 [CSS Modules](https://vue-loader.vuejs.org/en/features/css-modules.html) 或用一个额外的全局 `<style>` 元素手动设置类似 BEM 的作用域策略。

- **示例**：

  ```
  <div v-html="html"></div>
  ```

- **完整示例2**：

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
  </head>
  <body>
    <div id="app" v-html="message"></div>
    <script>
      const vm = new Vue({
        data: {
          message: '<h1>11111</h1>'
        }
      })
      vm.$mount(document.getElementById('app'))
    </script>
  </body>
  </html>
  ```

- `css modules`使用说明
  [css modules官网][https://cli.vuejs.org/zh/guide/css.html#css-modules]

  用新版的Vue-cli工具构建的vue项目，你可以通过 `<style module>` 以**开箱即用**的方式[在 `*.vue` 文件中使用 CSS Modules](https://vue-loader.vuejs.org/zh/guide/css-modules.html)。

  如果你希望自定义生成的 CSS Modules 模块的类名，可以通过 `vue.config.js` 中的 `css.loaderOptions.css` 选项来实现。所有的 `css-loader` 选项在这里都是支持的，例如 `localIdentName` 和 `camelCase`：

  ```js
  // vue.config.js
  module.exports = {
    css: {
      loaderOptions: {
        css: {
          // 注意：以下配置在 Vue CLI v4 与 v3 之间存在差异。
          // Vue CLI v3 用户可参考 css-loader v1 文档
          // https://github.com/webpack-contrib/css-loader/tree/v1.0.1
          modules: {
            localIdentName: '[name]-[hash]'
          },
          localsConvention: 'camelCaseOnly'
        }
      }
    }
  }
  ```

  使用示例：

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
      color: red;
    }
  </style>
  ```

  