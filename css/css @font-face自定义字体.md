# @font-face自定义字体

> @font-faces是CSS3推出的自定义字体的属性。

首先看一段google免费字体库Lobster字体的代码：
```css
/* cyrillic-ext */
@font-face {
  font-family: 'Lobster';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/lobster/v28/neILzCirqoswsqX9zo-mM4MwWJXNqA.woff2) format('woff2');
  unicode-range: U+0460-052F, U+1C80-1C88, U+20B4, U+2DE0-2DFF, U+A640-A69F, U+FE2E-FE2F;
}
/* cyrillic */
@font-face {
  font-family: 'Lobster';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/lobster/v28/neILzCirqoswsqX9zoamM4MwWJXNqA.woff2) format('woff2');
  unicode-range: U+0301, U+0400-045F, U+0490-0491, U+04B0-04B1, U+2116;
}
/* vietnamese */
@font-face {
  font-family: 'Lobster';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/lobster/v28/neILzCirqoswsqX9zo2mM4MwWJXNqA.woff2) format('woff2');
  unicode-range: U+0102-0103, U+0110-0111, U+0128-0129, U+0168-0169, U+01A0-01A1, U+01AF-01B0, U+1EA0-1EF9, U+20AB;
}
/* latin-ext */
@font-face {
  font-family: 'Lobster';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/lobster/v28/neILzCirqoswsqX9zoymM4MwWJXNqA.woff2) format('woff2');
  unicode-range: U+0100-024F, U+0259, U+1E00-1EFF, U+2020, U+20A0-20AB, U+20AD-20CF, U+2113, U+2C60-2C7F, U+A720-A7FF;
}
/* latin */
@font-face {
  font-family: 'Lobster';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/lobster/v28/neILzCirqoswsqX9zoKmM4MwWJU.woff2) format('woff2');
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}
```
上述代码自定义一个叫做`Lobster`的字体，从这个代码我们可以大概知道如何自定义一个字体。
* font-family ：定义字体名；
* font-style ：定义样式，normal / italic / oblique
* font-weight: 定义字体粗细
* src: 定义该字体下载的地址(指向一个或多个字体文件)
* unicode-range: 定义该字体支持Unicode字符的范围。默认值是"ü+0-10 FFFF"

Lobster的定义就是指明了在不同的字符范围，使用的是不同的字体文件。

<br>

下面是另外一个例子：
```css
@font-face {
    font-family: 'CiscoSans TT Extra Light';
    src: url(~@/styles/fonts/CiscoSansTTExtraLight.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTExtraLight.woff) format('woff');
    font-weight: 500;
    font-style: normal;
}

@font-face {
    font-family: 'CiscoSans TT Light';
    src: url(~@/styles/fonts/CiscoSansTTLight.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTLight.woff) format('woff');
    font-weight: 500;
    font-style: normal;
}

@font-face {
    font-family: 'CiscoSans TT Regular';
    src: url(~@/styles/fonts/CiscoSansTTRegular.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTRegular.woff) format('woff');
    font-weight: 500;
    font-style: normal;
}

@font-face {
    font-family: 'CiscoSans TT Bold';
    src: url(~@/styles/fonts/CiscoSansTTExtraLight.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTExtraLight.woff) format('woff'), url(~@/styles/fonts/CiscoSansTTLight.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTLight.woff) format('woff'), url(~@/styles/fonts/CiscoSansTTRegular.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTRegular.woff) format('woff'), url(~@/styles/fonts/CiscoSansTTBold.woff2) format('woff2'), url(~@/styles/fonts/CiscoSansTTBold.woff) format('woff');
    font-weight: 500;
    font-style: normal;
}
```
