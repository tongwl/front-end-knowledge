# 常用npm命令

* `npm init`
* `npm install`
* `npm install -g`
* `npm ci`
* `npm install --production`
* `npm audit`
* `npm create`



## `npm init`

初始化一个package.json。

npm 



## `npm install`

* `npm install --production`
* 

`npm install`会安装dependencies和devDependencies，并且会更新package-lock.json；

`npm install --production `只会安装dependencies，并且会更新package-lock.json，它是生产环境的选择。

## `npm ci`

#### 为什么要使用`npm ci`呢？

这里有一个案例：

最近出现了Jenkins构建成功，但是项目却运行失败的情况，排查问题的时候发现是Jenkins构建时安装依赖的版本不对导致的构建失败。

本身这种情况发生的概率比较小，因为我们在安装依赖的时候默认都会在依赖的版本号前面加上 `^` 来限制安装依赖的大版本。

这次的问题是在安装 `Taro` 时版本的不对齐导致的。如果我们需要使用 `Taro` ，除了安装 `@tarojs/cli` 之外，我们还需要安装一些依赖（使用 `Taro CLI` 创建项目时会自动安装），比如：`@tarojs/components`、 `@tarojs/runtime`、 `@tarojs/taro`等等。

`Taro` 要求我们在安装这些依赖的时候 `Taro CLI` 版本与项目中 `Taro` 相关依赖的版本保持一致，否则无法正常运行。关于这项规则的详情可以查阅[Taro 官方文档](https://link.juejin.cn?target=https%3A%2F%2Fnervjs.github.io%2Ftaro%2Fblog%2F2020-09-01-taro-versions%2F%23%E5%B0%86-taro-cli-%E7%89%88%E6%9C%AC%E4%B8%8E%E9%A1%B9%E7%9B%AE%E4%B8%AD-taro-%E7%9B%B8%E5%85%B3%E4%BE%9D%E8%B5%96%E7%9A%84%E7%89%88%E6%9C%AC%E4%BF%9D%E6%8C%81%E4%B8%80%E8%87%B4).

在我们的 `package.json` 中，我们只是使用了 `^` 来限制这些依赖的大版本更新，这样的限制对于 `Taro` 来说是不足够的。

原本我们的方案是去掉 `Taro` 相关依赖的前面的 `^`，这样这些依赖的版本就完全固定了，除非我们手动修改它，这样的方案当然是没有问题的。

但除此之外，我们应该还有另一个方案。别忘了，我们在本地运行和调试项目的时候是完全正常的，也就是说我们本地安装的依赖是没问题的。

所以另一个方案就是让 Jenkins 构建时安装和使用与我们本地相同的依赖，如果你说直接将 `node_modules` 推送到代码仓库中，这当然也能解决问题。但我不敢保证，你的同事听到你的这番言论会不会臭骂你两句，然后踹你一脚。

此时我们应该想到另一个可能被我们遗忘的小机灵鬼：`package-lock.json`。这可是控制 `node_modules` 文件夹中所有依赖的源头啊。

是的，我们完全可以通过锁定问题的源头来解决问题，即锁定 `package-lock.json`，让 Jenkins 在构建安装依赖时使用和我们本地依赖一致的包，这样我们的问题就可以解决了。

而实现这一目标的的一个优雅工具就是：`npm ci`.我们可以使用 `npm ci` 取代 `npm install`，将构建脚本里的 `npm install` 换成 `npm ci`，这样就可以实现我们所需要的效果了。

#### `npm ci`和`npm install`的主要区别

- 项目必须有 package-lock.json 或 npm-shrinkwrap.json，否则无法使用`npm ci`。
- 如果 package-lock.json 中的依赖项与 package.json 中的依赖项不匹配，则 `npm ci` 将退出并显示错误，而不是更新 package-lock.json。
- `npm ci` 一次只能安装整个项目：无法添加单个依赖项。
- `npm ci` 开始安装之前自动删除node_modules文件夹。
- `npm ci`不会更改package.json和package-lock.json，而是安全按照package-lock.json的版本进行下载安装。

> 学习：
>
> 由此我们知道，当我们进行CI/CD或生产发布时，最好使用`npm ci`，它会严格按照package-lock.json中指定版本进行安装，并且会对比package-lock.json和package.json依赖，防止由错误的依赖版本造成的故障。



## `npm install` vs `npm ci`

默认情况下，`npm install <package-name>` 将安装带有 `~`  `^` `latest` 版本号的软件包的相关最新版本。npm 项目上下文中的  `npm install` 会根据  `package.json` 规范将软件包下载到项目的  `node_modules` 文件夹中，从而升级软件包的版本，**并重新生成  `package-lock.json`** 。

`npm install` 使安装 JavaScript 软件包非常容易，以至于经常错误地使用此命令，导致npm包太大，太深。我们可以使用`npm install --production`标志来拯救！`npm install`会把dependencies和devDependencies都下载下来，但是在产线环境，devDependencies是不需要的，`npm install --production`只会安装dependencies的特性使得它是生产环境的良好选择。有时我们还有一个更好的选择，那就是`npm ci`，它更快，更严格，也更适合于放在 CI，生产环境中。



## `npm audit`

http://eux.baidu.com/blog/fe/npm%20aduit%E4%BA%8C%E4%B8%89%E4%BA%8B



npm@6新增了一大更新，就是新增了`npm audit`命令，该命令会在项目更新或者下载新的依赖包之后会自动运行，如果你在项目中使用了具有已知安全问题的依赖，就收到官方的警告通知。我们也可以手动输入`npx audit`查看依赖包的漏洞情况。为了复现，随便找了一个没那么官方的项目 [adminMongo](https://github.com/mrvautin/adminMongo) 并执行 `npm install` 试试，果然

```shell
found 5 vulnerabilities (2 low, 1 moderate, 1 high, 1 critical)
	run `npm audit fix` to fix them, or `npm audit` for details
```

再执行下 `npm audit` 查看详情，列表很清晰。

![alt](https://eux-public.bj.bcebos.com/2018/07/03/9D8FAEFD-85A5-45C2-8DCF-7B312AB9204D.png)



在我们的项目中，我们也可以使用npm audit查看安全漏洞，会得到以下类似提示：

```shell
found 489 vulnerabilities (46 low, 180 moderate, 179 high, 84 critical) in 2004 scanned packages
  run `npm audit fix` to fix 298 of them.
  190 vulnerabilities require semver-major dependency updates.
  1 vulnerability requires manual review. See the full report for details.
```

根据提醒，我们可以使用`npm audit fix`修复489个安全漏洞中的298个，执行`npm audit fix`后，我们会发现package.json和package-lock.json文件有所修改，修改的内容是修改了某些包的版本，比如：

```shell
-    "@amcharts/amcharts4": "^4.10.10"
+    "@amcharts/amcharts4": "^4.10.24"

-    "xlsx": "^0.15.1"
+    "xlsx": "^0.18.5"

-    "vue-moment": "^4.0.0"
+    "vue-moment": "^4.1.0"

-    "xlsx": "^0.15.1"
+    "xlsx": "^0.18.5"

-    "axios": "^0.18.0"
+    "axios": "^0.26.1"
```

**根据上述变动，我们能发现，`npm audit fix`的结果会修改次版本号和修订号，但是并不会修改主版本号，所以这个命令是个相对安全的命令。**

再次执行`npm audit`，得到以下提示：

```shell
found 204 vulnerabilities (45 low, 68 moderate, 71 high, 20 critical) in 2022 scanned packages
  run `npm audit fix` to fix 42 of them.
  162 vulnerabilities require semver-major dependency updates.
```

提示继续执行`npm audit fix`:

```shell
found 165 vulnerabilities (45 low, 39 moderate, 62 high, 19 critical) in 2026 scanned packages
  run `npm audit fix` to fix 11 of them.
  154 vulnerabilities require semver-major dependency updates.
```

提示继续执行`npm audit fix`:

```shell
found 157 vulnerabilities (45 low, 39 moderate, 54 high, 19 critical) in 2026 scanned packages
  run `npm audit fix` to fix 3 of them.
  154 vulnerabilities require semver-major dependency updates.
```

提示继续执行`npm audit fix`:

```shell
fixed 3 of 157 vulnerabilities in 2026 scanned packages
  8 package updates for 154 vulnerabilities involved breaking changes
  (use `npm audit fix --force` to install breaking changes; or refer to `npm audit` for steps to fix these manually)
```

终于执行结果不一样了，提示使用`npm audit fix --force`去修复，我们使用`npm audit fix --force`去执行：

```shell
fixed 157 of 157 vulnerabilities in 2026 scanned packages
  8 package updates for 154 vulnerabilities involved breaking changes
  (installed due to `--force` option)
```

提示继续执行`npm audit fix--force` :

```shell
fixed 0 of 0 vulnerabilities in 1872 scanned packages
```

漏洞终于修复完了，我们来看下package.json的修改：

```shell
-    "@vue/cli-plugin-babel": "^3.1.1",
+    "@vue/cli-plugin-babel": "^5.0.4",

-    "@vue/cli-plugin-e2e-nightwatch": "^3.1.1",
+    "@vue/cli-plugin-e2e-nightwatch": "^5.0.4",

-    "@vue/cli-plugin-eslint": "^3.1.5",
+    "@vue/cli-plugin-eslint": "^5.0.4",

-    "@vue/cli-plugin-unit-jest": "^3.1.1",
+    "@vue/cli-plugin-unit-jest": "^5.0.4",

-    "@vue/cli-service": "^3.1.4",
+    "@vue/cli-service": "^5.0.4",
```

**发现大版本号被修改了！！！** 这是个很危险的操作了，大版本的修改很有可能意味着代码不兼容了。所以是不推荐使用`npm audit fix--force`操作的。



