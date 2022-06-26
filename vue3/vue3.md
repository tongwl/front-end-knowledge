# vue3

* [组合式api](https://v3.cn.vuejs.org/guide/composition-api-introduction.html#%E7%8B%AC%E7%AB%8B%E7%9A%84-computed-%E5%B1%9E%E6%80%A7)

  

2. ## `ref`

   接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象仅有一个 `.value` property，指向该内部值。

   **示例：**

   ```js
   const count = ref(0)
   console.log(count.value) // 0
   
   count.value++
   console.log(count.value) // 1
   ```

   如果将对象分配为 ref 值，则它将被 [reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 函数处理为深层的响应式对象。



3. vue如何使用jsx

    先在dev-dependencies里面安装`@vitejs/plugin-vue-jsx`，然后在vite.config.ts中引入了`@vitejs/plugin-vue-jsx`，使用tsx是因为在tsconfig.json中设置了`"jsx": "preserve"`
   配置 vite.coonfig.ts:

   ```js
   import { defineConfig } from 'vite';
   import vue from '@vitejs/plugin-vue';
   import vueJsx from '@vitejs/plugin-vue-jsx';
   
   // https://vitejs.dev/config/
   export default defineConfig({
     plugins: [vue(), vueJsx({})],
   });
   ```

   配置tsconfig.json

   ```json
   {
     "compilerOptions": {
       "jsx": "preserve"
     }
   }
   ```

   

4. jsx语法：
   https://github.com/vuejs/babel-plugin-jsx/blob/dev/packages/babel-plugin-jsx/README-zh_CN.md

   [vue jsx语法](https://cn.vuejs.org/v2/guide/render-function.html)

5. 在vite.config.js中配置别名

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

   不要再使用 let path = require(‘path’)
   使用 `import path from 'path'` 进行引入

   

6. Vue3 jsx语法

   https://www.infoq.cn/article/opoczuurahozmpr9xkxr

   https://zhuanlan.zhihu.com/p/37920151

   https://www.yuque.com/zeka/vue/vu60wg	

   https://github.com/vuejs/babel-plugin-transform-vue-jsx

   https://juejin.cn/post/7007731144418394149

   https://juejin.cn/post/7001897686567747598

7. Template + setup的实现

   ```js
   <template>
     <div>
       <div>
         <div>Name</div>
         <input type="text" v-model="name" />
       </div>
       <div>
         <div>Sex</div>
         <input type="radio" name="sex" :checked="sex === Sex.male" @click="() => sex = Sex.male" />Male
         <input type="radio" name="sex" :checked="sex === Sex.female" @click="() => sex = Sex.female" />Female
       </div>
       <p>
         <button @click="handleSubmit">Submit</button>
       </p>
     </div>
   </template>
   
   <script setup lang="ts">
   import { onMounted, ref } from "vue";
   import { Sex, fetchUserInfo, updateUserInfo } from "../services";
   const name = ref("");
   const sex = ref(Sex.male);
   
   onMounted(() => {
     fetchUserInfo("id-xxx").then((res) => {
       name.value = res.name;
       sex.value = res.sex;
     });
   });
   
   const handleSubmit = () => {
     const params = { name: name.value, sex: sex.value };
     updateUserInfo(params).then((res) => {
       if (res) alert(JSON.stringify(params));
     });
   };
   
   </script>
   ```

   

8. next

   



