# setup

* 在 `setup` 中你应该避免使用 `this`，因为它不会找到组件实例。`setup` 的调用发生在 `data` property、`computed` property 或 `methods` 被解析之前，所以它们无法在 `setup` 中被获取。

* setup() 函数在组件创建 created() 之前执行。

* setup() 函数接收两个参数 props 和 context。

  第一个参数 props，它是响应式的，当传入新的 prop 时，它将被更新。

  第二个参数 context 是一个普通的 JavaScript 对象，它是一个上下文对象，暴露了其它可能在 setup 中有用的值。

* 