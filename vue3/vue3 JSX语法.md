# vue3 JSX语法

[官网参考](https://github.com/vuejs/babel-plugin-jsx/blob/dev/packages/babel-plugin-jsx/README-zh_CN.md)

* 绑定事件
* jsx好像没有teleport的功能





## 绑定事件

**绑定的事件名称前面加上on，事件名改为驼峰命名法并且首字母大写，拼接上前面的on即可绑定自定义事件。跟onClick绑定事件方式一致。**

```tsx
render() {
    return <>
      <button onClick={this.increase}>Increase</button>
    </>
  }
```

> 问题：
>
> 如何添加prevent等事件？
>
> 如何使用原生事件？





## jsx好像没有teleport的功能







##jsx的用法例子

1.

```jsx
const App = {
  render() {
    const tag = `h${this.level}`
    return <tag>{this.$slots.default}</tag>
  }
}
```



2.

错误写法：

```tsx
// 因为return后面是个空，所以返回的会是一个空的内容
render() {
  return
  <div>
    <div>aaaa</div>
  </div>
}
```

正确写法：

```tsx
// <div>应当紧跟着return这一行
render() {
  return <div>
    <div>aaaa</div>
  </div>
}
```

