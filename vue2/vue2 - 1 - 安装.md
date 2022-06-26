# 安装



## 兼容性

Vue不支持IE8及以下版本，因为Vue使用了IE8无法模拟的ES5特性。但它支持所有兼容ES5的浏览器。





## 直接使用script引入

**开发版本**：https://cn.vuejs.org/js/vue.js (包含完整的警告和调试模式)

**生产版本**：https://cn.vuejs.org/js/vue.min.js (删除了警告，33.46KB min+gzip)



## 使用CDN引入

对于制作原型或学习，你可以这样使用最新版本：

```script
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
```

对于生产环境，我们推荐链接到一个明确的版本号和构建文件，以避免新版本造成的不可预期的破坏：

```
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14"></script>
```

如果你使用原生 ES Modules，这里也有一个兼容 ES Module 的构建文件：

```
<script type="module">
  import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.esm.browser.js'
</script>
```



## NPM

在用 Vue 构建大型应用时推荐使用 NPM 安装[[1\]](https://cn.vuejs.org/v2/guide/installation.html#footnote-1)。NPM 能很好地和诸如 [webpack](https://webpack.js.org/) 或 [Browserify](http://browserify.org/) 模块打包器配合使用。同时 Vue 也提供配套工具来开发[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)。

```
# 最新稳定版
$ npm install vue
```



## 命令行工具 (CLI)

Vue 提供了一个[官方的 CLI](https://github.com/vuejs/vue-cli)，为单页面应用 (SPA) 快速搭建繁杂的脚手架。它为现代前端工作流提供了开箱即用的构建设置。只需要几分钟的时间就可以运行起来并带有热