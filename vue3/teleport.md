# teleport - 传送门

有时组件模板的一部分逻辑上属于该组件，而从技术角度来看，最好将模板的这一部分移动到 DOM 中 Vue app 之外的其他位置。

一个常见的场景是弹出框。弹出框的逻辑应该处于组件中，以便更好的和组件(业务页面)交互。但是弹出窗的html代码结构最好不要在组件中，因为它会受到组件css样式的限制，导致可能出现css样式问题，它最好是处于body下，以便更好的展示出一个弹出框的状态。此时`<teleport>`就可以帮助我们实现这个功能。



#### 语法

```vue
<teleport to="body" :disabled="false"/>
```

teleport有 `to` 和 `disabled` 两个属性，`to`决定挂载到哪个组件下；`disabled`决定传送门是否生效，`disabled`如果是true，则不会挂载到指定的`to`位置，而是直接挂载在父组件内。

- `to` - `string`。需要 prop，必须是有效的查询选择器或 HTMLElement (如果在浏览器环境中使用)。指定将在其中移动 `<teleport>` 内容的目标元素

```html
<!-- 正确 -->
<teleport to="#some-id" />
<teleport to=".some-class" />
<teleport to="[data-teleport]" />

<!-- 错误 -->
<teleport to="h1" />
<teleport to="some-string" />
```



- `disabled` - `boolean`。此可选属性可用于禁用 `<teleport>` 的功能，这意味着其插槽内容将不会移动到任何位置，而是在你在周围父组件中指定了 `<teleport>` 的位置渲染。

```html
<teleport to="#popup" :disabled="displayVideoInline">
  <video src="./my-movie.mp4">
</teleport>
```



请注意，<teleport>将移动实际的 DOM 节点，而不是被销毁和重新创建，并且它还将保持任何组件实例的活动状态。所有有状态的 HTML 元素 (即播放的视频) 都将保持其状态。



####基础例子

```vue
<template>
  <button @click="showModel">Show model</button>
  <teleport to="body"> <!-- 传送到body中 -->
    <div v-if="modelVisible">
      <h2>I am model</h2>
      <footer><button @click="hideModel">Hide model</button></footer>
    </div>
  </teleport>
</template>

<script>
import { ref } from 'vue'
export default {
  name: "Test8",
  setup() {
    const modelVisible = ref(false)
    const showModel = () => {
      modelVisible.value = true
    }
    const hideModel = () => {
      modelVisible.value = false
    }
    return {
      modelVisible,
      showModel,
      hideModel
    }
  }
}
</script>

<style scoped>
  div {
    background: #ccc;
    position: absolute;
    left: 50%;
    top: 50%;
  }
</style>
```



#### 与vue组件一起使用

FatherComponent.vue

```vue
<template>
  <header><button @click="show">Open model</button></header>
  <model @close-model="onCloseModel" :modelVisible="modelVisible">My model</model>
</template>

<script>
import { ref } from 'vue'
import model from './Model.vue'
export default {
  name: "Father",
  components: {model},
  setup() {
    const modelVisible = ref(false)
    const show = () => {
      modelVisible.value = true
    }

    const onCloseModel = () => {
      modelVisible.value = false
    }
    return {
      modelVisible,
      show,
      onCloseModel
    }
  }
}
</script>

<style scoped>

</style>
```



Model.vue

```vue
<template>
  <teleport to="body">
    <section v-if="modelVisible">
      <h3><slot>I am model</slot></h3>
      <div><button @click="hide">Close model</button></div>
    </section>
  </teleport>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "Model",
  props: {
    modelVisible: Boolean,
  },
  emits: {
    'close-model': null // 表示不验证
  },
  setup(props, context) {
    const hide = () => {
      context.emit('close-model')
    }
    return {
      hide
    }
  }
})
</script>

<style scoped>
section {
  position: absolute;
  left: 50%;
  top: 50%;
  background: #333;
}
</style>
```





####在同一目标上使用多个 teleport

一个常见的用例场景是一个可重用的 `<Modal>` 组件，它可能同时有多个实例处于活动状态。对于这种情况，多个 `<teleport>` 组件可以将其内容挂载到同一个目标元素。顺序将是一个简单的追加——稍后挂载将位于目标元素中较早的挂载之后。

```html
<teleport to="#modals">
  <div>A</div>
</teleport>
<teleport to="#modals">
  <div>B</div>
</teleport>

<!-- result-->
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```



#### tsx的teleport不生效

**好像tsx的teleport并没有效果，不知道为什么。**

```jsx
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: 'Test7',
  setup() {
    const modelVisible = ref(false)

    const showModel = () => {
      modelVisible.value = true
    }

    const hideModel = () => {
      modelVisible.value = false
    }

    return {
      modelVisible,
      showModel,
      hideModel
    }
  },
  render() {
     return <div>
       <button onClick={this.showModel}>Show model</button>
       <teleport to="body"> { /* teleport在jsx中并不会生效 */ }
         <div v-show={this.modelVisible} style={{background: '#ccc', position: 'absolute', left: '50%', top: '50%'}}>
           <h1>I am modal</h1>
           <footer>
             <button onClick={this.hideModel}>Close model</button>
           </footer>
         </div>
       </teleport>
     </div>
  }
})
```





>  当您使用模态框、下拉列表或通知元素时，Teleport 尤其方便，这些元素在 DOM 中的顺序很重要。