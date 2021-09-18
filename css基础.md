# css基础

* 1.css引入外部字体
* 2.font-family说明
* 3.border说明
* 4.border-radius说明
* 5.margin可以设置负值
* 6.margin, padding
* 7.选择器总结
* 8.单位 (px, em, rem)
* 9.颜色表示法
* 10.使用CSS自定义属性（变量）
* 11 :root 伪类选择器
* 12. ~~媒体查询~~
  13. 





## 1.css引入外部字体

以下以引入google字体为例子。

[Google 字体库](https://fonts.google.com/)是一个免费的 Web 字体库，我们只需要在 CSS 里设置字体的 URL 即可使用。

要引入 Google Font，你需要从 Google Fonts 上复制字体的 URL，并粘贴到你的 HTML 里面。 在这个挑战中，我们需要引入 `Lobster` 字体。 因此，请复制以下代码段，并粘贴到代码编辑器顶部，即放到 `style` 标签之前。

```html
<link href="https://fonts.googleapis.com/css?family=Lobster" rel="stylesheet" type="text/css">
```



## 2.font-family说明

```css
font-family: FAMILY_NAME, GENERIC_NAME;
```

* GENERIC_NAME 是可选的，它用来指明在其他字体不可用时的后备字体。
* 如果字体名含有空格，则在声明时需要用引号包起来。 例如，使用 `"Open Sans"` 字体需要添加引号，而 `Lobster` 则不需要。
* 所有浏览器都有几种默认字体， 包括 `monospace`、`serif` 和 `sans-serif`。



## 3.border说明

MDN参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/border

[CSS](https://developer.mozilla.org/en-US/docs/CSS)的border属性是一个用于设置各种单独的边界属性的[简写属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Shorthand_properties)。`border可以用于设置一个或多个以下属性的值：` 

* [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width)
*  [`border-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-style)
*  [`border-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-color)

```css
border: solid;
border: dashed red;
border: 1rem solid;
border: thick double #32a1ce;
border: 4mm ridge rgba(170, 50, 220, .6);
```

border-style所有值

关键字用于描述边框样式。它可以有以下取值：

| `none`       |      | 和关键字 `hidden` 类似，不显示边框。在这种情况下，如果没有设定背景图片，[`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算后的值将是 `0`，即使先前已经指定过它的值。在单元格边框重叠情况下，`none` 值优先级最低，意味着如果存在其他的重叠边框，则会显示为那个边框。 |
| ------------ | ---- | ------------------------------------------------------------ |
| `hidden`     |      | 和关键字 `none` 类似，不显示边框。在这种情况下，如果没有设定背景图片，[`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算后的值将是 `0`，即使先前已经指定过它的值。在单元格边框重叠情况下，`hidden` 值优先级最高，意味着如果存在其他的重叠边框，边框不会显示。 |
| **`dotted`** |      | 显示为一系列圆点。标准中没有定义两点之间的间隔大小，视不同实现而定。圆点半径是 [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 计算值的一半。 |
| **`dashed`** |      | 显示为一系列短的方形虚线。标准中没有定义线段的长度和大小，视不同实现而定。 |
| **`solid`**  |      | 显示为一条实线。                                             |
| `double`     |      | 显示为一条双实线，宽度是 [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width) 。 |
| `groove`     |      | 显示为有雕刻效果的边框，样式与 `ridge` 相反。                |
| `ridge`      |      | 显示为有浮雕效果的边框，样式与 `groove` 相反。               |
| `inset`      |      | 显示为有陷入效果的边框，样式与 `outset` 相反。当它指定到 [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) 为 `collapsed` 的单元格时，会显示为 `groove` 的样式。 |
| `outset`     |      | 显示为有突出效果的边框，样式与 `inset` 相反。当它指定到 [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) 为 `collapsed` 的单元格时，会显示为 `ridge` 的样式。 |



## 4.border-radius说明

```css
border-raduis: 50%;  //圆，椭圆
border-radius: 5px 10px; //左上角和右下角 右上角和左下角
border-radius: 1px 2px 3px; //左上角	右上角和左下角	右下角
border-radius: 1px 2px 3px 4px; //左上角	右上角	右下角	左下角	
border-radius: 20% / 10%;	//作用于4个角，第一个值是横轴的百分比，第二个值是纵轴的百分比
border-raduis: 50% / 10% 20px 30px; // 50% / 10%是指左上角横轴50%, 纵轴10%，20px是指右上角和左下角，30px是指右下角
```



## 5.margin可以设置负值



## 6.margin, padding

```css
margin: 1px; //top right bottom left都是1px
margin: 1px 2px; //top, bottom是1px, right和left是2px
margin: 1px 2px 3px; //top是1px, right和left是2px, bottom是3px
margin: 1px 2px 3px 4px; //top是1px, right是2px, bottom是3px, left是4px
```



## 7.选择器总结

​	MDN参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors

* 通用选择器
  *

  ```css
  body * {
  	font-size: 16px;
  }
  ```

* 元素选择器

  ```css
  div {
  	font-size: 16px;
  }
  ```

* id选择器  #id

  ```css
  #id1 {
  	font-size: 16px;
  }
  ```

* class选择器 .class

  ```css
  .class1 {
  	font-size: 16px;
  }
  ```

* 属性选择器 [attr=value]
  
  ```css
  [type="radio"] {
  	margin-bottom: 10px;
  }
  ```
  
* 分组选择器

  ```css
  div, p {
  	color: red;
  }
  ```

* 后代选择器

  ```css
  div span {
  	color: red;
  }
  ```

* 直接子代选择器

   组合器选择前一个元素的直接子代的节点。

  ```
  div > span {
  	color: red;
  }
  ```

* 一般兄弟选择器

  `~` 组合器选择兄弟元素，也就是说，后一个节点在前一个节点后面的任意位置，并且共享同一个父节点。
  **语法**：`A ~ B`
  **例子**：`p ~ span` 匹配同一父元素下，`p`元素后的所有`span`元素。

  ```css
  p ~ span {
  	color: red;
  }
  ```

* 紧邻兄弟选择器

  `+` 组合器选择相邻元素，即后一个元素紧跟在前一个之后，并且共享同一个父节点。
  **语法：**`A + B`
  **例子：**`h2 + p` 会匹配所有紧邻在`h2`元素后的`p` 元素。

  ```css
  h2 + p {
  	color: red;
  }
  ```

* 伪类选择器
  MDN参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes

  * “爱恨原则”（LoVe/HAte）
    `:link`,`:visited`, `:hover`, `:active` 
  * 
    
    :first-child
    :last-child
    :nth-child

* 伪元素选择器

  * 

	#### 选择器优先级

**内联样式的优先级高于 ID 选择器**

```html
<style>
	#id1 {
	  color: red;
	}
</style>
<div id="id1" style="color: green;">tongwl</div>   <!--颜色呈现绿色-->
```



**Important 的优先级最高**





## 8.单位 (px, em, rem)



## 9.颜色表示法

```css
red
green
blue
#ffffff
#f00
rgb(0, 0, 0) //黑色
rgb(255, 255, 255) //白色
```



**`#ffffff`和`rgb(255, 255, 255)`的转换**

ff代表16进制，转换成10进制就是  f*16+f, f代表15，所以就是15\*16 + 15 =255，所以对应rgb的255。

再举个例子：

将#1a1b1c转换为rgb值

1a = 1*16+10 = 26；

1b = 1*16+11 = 27；

1c = 1*16+12 = 28；

所以转换成rgb的结果是rgb(26, 27, 28)。



## 10.使用CSS自定义属性（变量）

[MDN参考学习][https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_custom_properties#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%AE%9A%E4%B9%89%E5%B1%9E%E6%80%A7%E7%9A%84%E7%AC%AC%E4%B8%80%E6%AD%A5]

####申明一个变量

属性名需要以两个减号（`--`）开始，属性值则可以是任何有效的CSS值。

```css
element {
  --main-bg-color: brown;
}
```

注意，规则集所指定的选择器定义了自定义属性的可见作用域。通常的最佳实践是定义在根伪类 [`:root`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:root) 下，这样就可以在HTML文档的任何地方访问到它了：

```css
:root {
  --main-bg-color: brown;
}
```

**注意：**自定义属性名是大小写敏感的，`--my-color` 和 `--My-color` 会被认为是两个不同的自定义属性。

#### 使用自定义属性

使用一个局部变量时用 [`var()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/var()) 函数包裹以表示一个合法的属性值。

```html
<style>
  :root {
    --font-size: 48px;
  }

  div {
    font-size: var(--font-size);
  }

  p {
    font-size: var(--font-size);
  }

  span {
    font-size: var(--font-size);
  }
</style>
<div>div</div>
<p>p</p>
<span>span</span>
```

自定义属性可以继承和覆盖，当创建一个变量时，变量会在创建变量的选择器里可用。 同时，在这个选择器的后代选择器里也是可用的。 这是因为 CSS 变量是可继承的，和普通的属性一样。

例子：

```html
<style>
  :root {
    --color: red;
  }

  body {
    color: var(--color);
  }

  div {
    --color: blue;
  }

  div p {
    color: var(--color);
  }
</style>

<div>
  <p>我是蓝色</p>
  <span>我是红色</span>
</div>
<p>我是红色</p>
```

#### 自定义属性备用值

函数的第一个参数是[自定义属性](https://www.w3.org/TR/css-variables/#custom-property)的名称。如果提供了第二个参数，则表示备用值，当[自定义属性](https://www.w3.org/TR/css-variables/#custom-property)值不存在时生效。第二个参数可以嵌套，但是不能继续平铺展开下去了，例如：

```css
.two {
  color: var(--my-var, red); /* Red if --my-var is not defined */
}

.three {
  background-color: var(--my-var, var(--my-background, pink)); /* pink if --my-var and --my-background are not defined */
}

.three {
  background-color: var(--my-var, --my-background, pink); /* Invalid: "--my-background, pink" */
}
```

例子：

```html
<style>
  :root {
    --color: #f00;
  }

  body {
    color: var(--color2, blue); <!----color2不存在，所以会使用blue-->
  }
</style>
<p>111</p> <!--blue-->
```



## 11 :root 伪类选择器

`:root` 是一个伪类选择器，它是一个能够匹配文档根元素的选择器，通常指的是 `html` 元素。

