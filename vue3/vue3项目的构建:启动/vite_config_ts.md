# vite.config.ts

* 使用vite.config.js配置别名







## 使用vite.config.js配置别名

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],  // 注册插件
  server: {
    open: true
  },
  resolve: {
    alias: {
      // 如果报错__dirname找不到，需要安装node,执行yarn add @types/node --save-dev
      "@": path.resolve(__dirname, "src"),
      "comps": path.resolve(__dirname, "src/components"),
    }
  }
})
```

1、alias配置别名

如果报错__dirname找不到，需要安装node,
执行`yarn add @types/node --save-dev`

2、关于path的引入

不要再使用 let path = require(‘path’)，应该使用 `import path from 'path'` 进行引入。