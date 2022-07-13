# html5

* 浏览器支持
* 申明一个html页面
* 新增语义化标签
* 可以自定义标签
* main， figure?
* 



## 浏览器支持

* 现代的浏览器都支持 HTML5。
* 此外，所有浏览器，包括旧的和最新的，对无法识别的元素会作为**内联元素**自动处理。

## 申明一个html页面

```html
<!DOCTYPE html>
```

* `<!DOCTYPE>`不区分大小写

* `<!DOCTYPE>`没有结束标签

* `<!DOCTYPE>`必须申明在文档的第一行

  

下面是一个简单的HTML5文档：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

</body>
</html>
```

**对于中文网页需要使用 <meta charset="utf-8"> 声明编码，否则会出现乱码。**



## 语义化标签

```html
<header></header>	
<nav></nav>
<article></article>
<section></section>	
<aside></aside>
<footer></footer>
```

![img](https://www.runoob.com/wp-content/uploads/2013/07/html5-layout.jpg)



## 自定义标签

你可以为html添加新的自定义标签，默认新增的标签是`行内元素`。

例子1:

```html
<body>
  <my-haha id="myHaha">1111</my-haha>
  <script>
    console.log(window.getComputedStyle(document.getElementById('myHaha')).getPropertyValue('display')); //"line"
  </script>
</body>
```

例子2：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>
    myHaha {
      display: block;
      background-color: #ddd;
      padding: 50px;
      font-size: 30px;
    }
  </style>
</head>
<body>
  <myHaha id="myHaha">1111</myHaha>
  <script>
    console.log(window.getComputedStyle(document.getElementById('myHaha')).getPropertyValue('display')); //"block"
  </script>
</body>
</html>
```

