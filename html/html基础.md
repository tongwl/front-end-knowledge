# basic html

* **img**
* **a**
* **form**
* **input  type="text"**
* **button** 
* **label** 
* **input radio**
* \<!DOCTYPE html\> 声明这个文件是html5文件
* 









## img

`<img src = "./bird.png" alt="bird">`

The text inside an `alt` attribute is used for screen readers to improve accessibility and is displayed if the image fails to load.

当我们设置img的时候，最好不要忘记设计`alt`.



## a

链接

target = _self / _blank / ...

```html
<a href="https://www.freecatphotoapp.com" target="_blank">cat photos</a>
```

页面导航功能

```html
<a href="#contacts-header">Contacts</a>
...
<h2 id="contacts-header">Contacts</h2>
```

​	

# form 

# input  type="text"

# button

```html
<form action="https://www.freecatphotoapp.com/submit-cat-photo">
  <input type="text" placeholder="cat photo URL" required>
  <button type="submit">submit</button> 
</form>
```



# label 

```html
<label> 
  <input type="radio" name="indoor-outdoor">Indoor 
</label>
```

```html
<input id="indoor" type="radio" name="indoor-outdoor">
<label for="indoor">Indoor</label>
```

```html
<label for="indoor"> 
  <input id="indoor" type="radio" name="indoor-outdoor">Indoor 
</label>
```



#input radio

属性

* type="radio"
* name="username"  //当在form表中中，submit提交的时候，传出去的值就是name=value的格式，例如username="tongwl"
* value="tongwl"  //当在form表中中，submit提交的时候，传出去的值就是name=value的格式，例如username="tongwl"
* checked  //默认是否选中

```html
<label>
	<input type="radio" name="indoor-outdoor" value="indoor">indoor
</label>
<label>
	<input type="radio" name="indoor-outdoor" value="outdoor">outdoor
</label> 
```



#input checkbox

属性

* type="checkbox"
* name="user"  //当在form表中中，submit提交的时候，传出去的值就是name=value的格式，例如user="tongwl,lily"
* value="lily"  //当在form表中中，submit提交的时候，传出去的值就是name=value的格式，例如user="tongwl,lily"
* checked  //默认是否选中

```html
<label for="loving">
  <input id="loving" type="checkbox" name="personality" value="loving"> Loving
</label>
<label for="lazy">
  <input id="lazy" type="checkbox" name="personality" value="lazy"> Lazy
</label>
<label for="energetic">
  <input id="energetic" type="checkbox" name="personality" value="energetic"> Energetic
</label>
```



# 声明一个html5文件

```
<!DOCTYPE html>  <!--声明这个文件是html5文件-->
<html>
	<head>  <!--Metadata elements,such as link, meta, title, and style, typically go inside the head element.-->
    <meta />
    <title>The best page ever</title>
  </head>
  <body>
    <div>
    </div>
  </body>
</html>
```

