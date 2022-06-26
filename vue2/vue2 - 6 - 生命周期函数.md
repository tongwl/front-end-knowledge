# vue - 生命周期函数

created：在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。

mounted：在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作

其实这两者比较好理解，通常created使用的次数多，而mounted通常是在一些插件的使用或者组件的使用中进行操作，比如插件chart.js的使用: `var ctx = document.getElementById(ID)`;通常会有这一步，而如果你写入组件中，你会发现在created中无法对chart进行一些初始化配置，一定要等这个html渲染完后才可以进行，那么mounted就是不二之选。

### 生命周期参考

![Vue 实例生命周期](https://cn.vuejs.org/images/lifecycle.png)



