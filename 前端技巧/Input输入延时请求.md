# Input输入延时请求

在有些情况下，我们需要在输入框输入内容后触发某些行为，特别是需要触发api的时候，一般会设置input输入延迟触发请求，以避免短时间内发送多个api，造成性能浪费。我们可以用两种方式实现延时请求。

1.使用clearTimeout

```javascript
var timeoutId = 0;
$('#text').off('keyup').on('keyup', function (event) {
  clearTimeout(timeoutId);
  timeoutId = setTimeout(function () {
    console.log('do something like send api request.');
  }, 1000);
});
```



2.使用闭包

以vue语法为例子

```vue
 <el-input v-model="user" @input="searchCityModel($event)"/>
 
// 选择城市的v-model
searchCityModel(val) {
  this.lastTimer = val; // lastTimer为全局变量
  setTimeout(() => {
    if(this.lastTimer === val)
    //如果this.lastTimer === val（也就是你停止输入0.5s之内都没有其它的值发生变化）则做你想要做的事
    {
      this._sendGetSearchTopTipServer(val);
    }
  },500)
 
}
```



