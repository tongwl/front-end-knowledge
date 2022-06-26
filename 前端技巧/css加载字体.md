# css加载字体
## 引用外部字体
以下以引入google字体为例子。

Google 字体库是一个免费的 Web 字体库，我们只需要在 CSS 里设置字体的 URL 即可使用。

要引入 Google Font，你需要从 Google Fonts 上复制字体的 URL，并粘贴到你的 HTML 里面。 在这个例子中，我们需要引入 Lobster 字体。 因此，请复制以下代码段，并粘贴到代码编辑器顶部，即放到 style 标签之前。
```html
<link href="https://fonts.googleapis.com/css?family=Lobster" rel="stylesheet" type="text/css">
```

## 下载woff并自定义字体
当我们的项目场景不适合引入线上的字体库，我们可以单独找到woff/woff2文件，下载并在本地自定义字体使用。
参考上述 https://fonts.googleapis.com/css?family=Lobster 的代码，我们可以看看字体是如何定义的：
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

我们可以把上述的woff / woff2文件下载到本地，然后写好上述代码即可。