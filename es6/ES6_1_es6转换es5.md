# ES6_1_es6转换es5



## 背景



###一. ECMAScript 和 JavaScript 的关系

ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现。



### 二. [ES6和ECMAScript2015的关系][https://es6.ruanyifeng.com/#docs/intro]

ES6 既是一个历史名词，也是一个泛指，含义是 5.1 版以后的 JavaScript 的下一代标准，涵盖了 ES2015、ES2016、ES2017 等等，而 ES2015 则是正式名称，特指该年发布的正式版本的语言标准。



### 三. ES6代码转换为ES5代码

目前，各大浏览器对 ES6 的支持可以查看[kangax.github.io/compat-table/es6/](https://kangax.github.io/compat-table/es6/)。
针对一些浏览器的老版本，它们对ES6的支持性并不好，所以一般都会将ES6转换为ES5使用。

#### [Babel转换器][https://www.babeljs.cn/docs/usage]

Babel是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在老版本的浏览器执行。	

截止2020/02/22整个配置过程包括：

1. 运行以下命令安装所需的包（package）：

   ```sh
   npm install --save-dev @babel/core @babel/cli @babel/preset-env
   npm install --save @babel/polyfill
   ```

2. 在项目的根目录下创建一个命名为 `babel.config.json` 的配置文件（需要 `v7.8.0` 或更高版本），并将以下内容复制到此文件中：

   ```json
   {
     "presets": [
       [
         "@babel/env",
         {
           "targets": {
             "edge": "17",
             "firefox": "60",
             "chrome": "67",
             "safari": "11.1"
           },
           "useBuiltIns": "usage",
           "corejs": "3.6.5"
         }
       ]
     ]
   }
   ```

   > 上述浏览器列表仅用于示例。根据你所需要支持的浏览器进行调整。

   如果你使用的是 Babel 的旧版本，则文件名为 `babel.config.js`。

   ````javascript
   const presets = [
     [
       "@babel/env",
       {
         targets: {
           edge: "17",
           firefox: "60",
           chrome: "67",
           safari: "11.1",
         },
         useBuiltIns: "usage",
         "corejs": "3.6.4",
       },
     ],
   ];
   
   module.exports = { presets };
   ````

   

3. 运行此命令将 `src` 目录下的所有代码编译到 `lib` 目录：

   ````sh
   ./node_modules/.bin/babel src --out-dir lib
   ````

   你可以利用 npm@5.2.0 所自带的 npm 包运行器将 `./node_modules/.bin/babel` 命令缩短为 `npx babel`