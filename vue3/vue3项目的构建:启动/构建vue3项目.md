# 构建vue3项目



##核心步骤

使用以下步骤即可构建`vue3  ` + `typescript`  + `tsx` + `scss` + `css modules` + ~~`eslint`~~ + ~~`prettier`~~

```shell
# Step1: 构建vue3 + typescript项目
npm create vite@latest my-vue-app -- --template vue-ts

# Step2: tsx的支持
	# 1.安装@vitejs/plugin-vue-jsx插件
	npm install @vitejs/plugin-vue-jsx --save-dev  
	# 2. 在vite.config.ts的plugins中添加入@vitejs/plugin-vue-jsx，生成以下vite.config.ts文件
		import { defineConfig } from 'vite'
		import vue from '@vitejs/plugin-vue'
		import vueJsx from '@vitejs/plugin-vue-jsx'

		// https://vitejs.dev/config/
		export default defineConfig({
  		plugins: [vue(), vueJsx()]
		})
	# 3. 在tsconfig.json中设置"jsx": "preserve"，生成以下tsconfig.json文件：（默认就有）
	{
  	"compilerOptions": {
    	"target": "esnext",
    	"useDefineForClassFields": true,
    	"module": "esnext",
    	"moduleResolution": "node",
    	"strict": true,
    	"jsx": "preserve",
    	"sourceMap": true,
    	"resolveJsonModule": true,
    	"isolatedModules": true,
    	"esModuleInterop": true,
    	"lib": ["esnext", "dom"],
    	"skipLibCheck": true
  	},
  	"include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
  	"references": [{ "path": "./tsconfig.node.json" }]
	}
	
# Step3: scss的支持 (只需要安装预处理器即可)
	# .scss and .sass
	npm add -D sass

# Step4: css modules的支持：你还可以通过在文件扩展名前加上 .module 来结合使用 CSS modules 和预处理器，例如 style.module.scss。

# Step5: 

```



##详情

将 Vue.js 添加到项目中主要有四种方式：

1. 在页面上以 [CDN 包](https://v3.cn.vuejs.org/guide/installation.html#cdn)的形式导入。
2. 下载 JavaScript 文件并[自行托管](https://v3.cn.vuejs.org/guide/installation.html#下载并自托管)。
3. 使用 [npm](https://v3.cn.vuejs.org/guide/installation.html#npm) 安装它。
4. 使用官方的 [CLI](https://v3.cn.vuejs.org/guide/installation.html#命令行工具-cli) 来构建一个项目，它为现代前端工作流程提供了功能齐备的构建设置 (例如，热重载、保存时的提示等等)。

详细请参考[官网安装教程](https://v3.cn.vuejs.org/guide/installation.html#%E5%8F%91%E5%B8%83%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)。

我们着重介绍使用vite创建vue3项目[vite官网参考](https://cn.vitejs.dev/guide/#scaffolding-your-first-vite-project) [github参考](https://github.com/vitejs/vite/tree/main/packages/create-vite)

###Step1

**构建一个 Vite + Vue 项目，运行:**

```shell
# npm 6.x
npm create vite@latest my-vue-app --template vue

# npm 7+, extra double-dash is needed:
npm create vite@latest my-vue-app -- --template vue

# yarn
yarn create vite my-vue-app --template vue

# pnpm
pnpm create vite my-vue-app -- --template vue
```

目前支持以下几种模板类型：

- `vanilla`
- `vanilla-ts`
- `vue`
- `vue-ts`
- `react`
- `react-ts`
- `preact`
- `preact-ts`
- `lit`
- `lit-ts`
- `svelte`
- `svelte-ts`

**以npm7+版本构建vue3 js版本为例，使用以下命令即可成功：**

```shell
npm create vite@latest my-vue-app -- --template vue
```

**如果需要构建vue3 ts版本，使用以下命令即可成功：**

```shell
npm create vite@latest my-vue-app -- --template vue-ts
```



### Step2

**tsx的支持**

1. 首先安装`@vitejs/plugin-vue-jsx`依赖：

```
npm install @vitejs/plugin-vue-jsx --save-dev  	
```

2. 在`vite.config.ts`的plugins中添加入`@vitejs/plugin-vue-jsx`，生成以下`vite.config.ts`文件：

   ```typescript
   import { defineConfig } from 'vite'
   import vue from '@vitejs/plugin-vue'
   import vueJsx from '@vitejs/plugin-vue-jsx'
   
   // https://vitejs.dev/config/
   export default defineConfig({
     plugins: [vue(), vueJsx()]
   })
   ```

3. 在`tsconfig.json`中设置`"jsx": "preserve"`，生成以下`tsconfig.json`文件：（默认就有）

   ```json
   {
     "compilerOptions": {
       "target": "esnext",
       "useDefineForClassFields": true,
       "module": "esnext",
       "moduleResolution": "node",
       "strict": true,
       "jsx": "preserve",
       "sourceMap": true,
       "resolveJsonModule": true,
       "isolatedModules": true,
       "esModuleInterop": true,
       "lib": ["esnext", "dom"],
       "skipLibCheck": true
     },
     "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
     "references": [{ "path": "./tsconfig.node.json" }]
   }
   ```

   

### Step3

**scss的支持 (只需要安装预处理器即可)**

```shell
# .scss and .sass
npm add -D sass
```

[vite官网参考](https://cn.vitejs.dev/guide/features.html#css)

####CSS

导入 `.css` 文件将会把内容插入到 `<style>` 标签中，同时也带有 HMR 支持。也能够以字符串的形式检索处理后的、作为其模块默认导出的 CSS。

####`@import` 内联和变基

Vite 通过 `postcss-import` 预配置支持了 CSS `@import` 内联，Vite 的路径别名也遵从 CSS `@import`。换句话说，所有 CSS `url()` 引用，即使导入的文件在不同的目录中，也总是自动变基，以确保正确性。

Sass 和 Less 文件也支持 `@import` 别名和 URL 变基（具体请参阅 [CSS Pre-processors](https://cn.vitejs.dev/guide/features.html#css-pre-processors)）。

####PostCSS

如果项目包含有效的 PostCSS 配置 (任何受 [postcss-load-config](https://github.com/postcss/postcss-load-config) 支持的格式，例如 `postcss.config.js`)，它将会自动应用于所有已导入的 CSS。

####CSS Modules

任何以 `.module.css` 为后缀名的 CSS 文件都被认为是一个 [CSS modules 文件](https://github.com/css-modules/css-modules)。导入这样的文件会返回一个相应的模块对象：

```tsx
/* example.module.css */
.red {
  color: red;
}
import classes from './example.module.css'
document.getElementById('foo').className = classes.red
```

CSS modules 行为可以通过 [`css.modules` 选项](https://cn.vitejs.dev/config/#css-modules) 进行配置。

如果 `css.modules.localsConvention` 设置开启了 camelCase 格式变量名转换（例如 `localsConvention: 'camelCaseOnly'`），你还可以使用按名导入。

```tsx
// .apply-color -> applyColor
import { applyColor } from './example.module.css'
document.getElementById('foo').className = applyColor
```

####CSS 预处理器

由于 Vite 的目标仅为现代浏览器，因此建议使用原生 CSS 变量和实现 CSSWG 草案的 PostCSS 插件（例如 [postcss-nesting](https://github.com/jonathantneal/postcss-nesting)）来编写简单的、符合未来标准的 CSS。

话虽如此，但 Vite 也同时提供了对 `.scss`, `.sass`, `.less`, `.styl` 和 `.stylus` 文件的内置支持。没有必要为它们安装特定的 Vite 插件，但必须安装相应的预处理器依赖：

```shell
# .scss and .sass
npm add -D sass

# .less
npm add -D less

# .styl and .stylus
npm add -D stylus
```

如果使用的是单文件组件，可以通过 `<style lang="sass">`（或其他预处理器）自动开启。

Vite 为 Sass 和 Less 改进了 `@import` 解析，以保证 Vite 别名也能被使用。另外，`url()` 中的相对路径引用的，与根文件不同目录中的 Sass/Less 文件会自动变基以保证正确性。

由于 Stylus API 限制，`@import` 别名和 URL 变基不支持 Stylus。

你还可以通过在文件扩展名前加上 `.module` 来结合使用 CSS modules 和预处理器，例如 `style.module.scss`。