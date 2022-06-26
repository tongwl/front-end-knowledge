# 关于npm的package.json，你知道多少?

参考文档：

[关于npm的package.json，你知道多少?](https://mp.weixin.qq.com/s/csGiBBvsZLI76yrXjD6NGg)

[npm dependencies/devDependencies... 详解](https://segmentfault.com/a/1190000022559498)

[npm 依赖管理中被忽略的那些细节](https://www.zoo.team/article/npm-details)

## 必须属性

#### name

name很容易理解，就是项目的名称，它是一个字符串。在给name字段命名时，需要注意以下几点：

- 名称的长度必须小于或等于214个字符，**不能以“.”和“_”开头，不能包含大写字母**（这是因为当软件包在npm上发布时，会基于此属性获得自己的URL，所以不能包含非URL安全字符（non-url-safe））；
- **名称可以作为参数被传入require("")，用来导入模块，所以应当尽可能的简短、语义化；**
- 名称不能和其他模块的名称重复，可以使用`npm view`命令查询当前模块是否已经存在，如果不存在就会提示404，如果npm包上有对应的包，则会显示包的详细信息。

实际上，我们平时开发的很多项目并不会发布在npm上，所以这个名称是否标准可能就不是那么重要，它不会影响项目的正常运行。如果需要发布在npm上，name字段一定要符合要求。

> 学习：
>
> 1. 当我们输入`npm view`的时候，npm会在当前目录寻找package.json文件，并且找到package.json文件里面的name属性，并返回是否在npm库中存在该名称的库的信息。所以它可以用来判断npm库是否已存在该包名。
> 1. 也可以输入`npm view test`来查看test名称是否被占用



#### version

version字段表示该项目包的版本号，它是一个字符串。在每次项目改动后，即将发布时，都要同步的去更改项目的版本号。版本号的使用规范如下：

- 版本号的命名遵循语义化版本2.0.0规范，格式为：**主版本号.次版本号.修订号**，通常情况下，修改主版本号是做了大的功能性的改动，修改次版本号是新增了新功能，修改修订号就是修复了一些bug；
- 如果某个版本的改动较大，并且不稳定，可能无法满足预期的兼容性需求，就需要发布先行版本，先行版本通常会加在版本号的后面，通过“-”号连接以点分隔的标识符和版本编译信息：内部版本（alpha）、公测版本（beta）和发布候选版本（rc，即release candiate）。

可以通过以下命令来查看npm包的版本信息，以react为例：

```json
// 查看最新版本: '17.0.2'
npm view react version


// 查看所有版本
/*
'16.14.0',
  '17.0.0-rc.0',
  '17.0.0-rc.1',
  '17.0.0-rc.2',
  '17.0.0-rc.3',
  '17.0.0',
  '17.0.1',
  '17.0.2',
  '18.0.0-alpha-00ced1e2b-20211102',
  '18.0.0-alpha-01be61c12',
  '18.0.0-alpha-02f411578-20211019',
  '18.0.0-alpha-031abd24b-20210907',
  '18.0.0-alpha-05726d72c-20210927',
  '18.0.0-alpha-0883c4cd3-20210929',
   ...
  '18.0.0-rc.2-next-577f2de46-20220320',
  '18.0.0-rc.2-next-581f0c42e-20220310',
  '18.0.0-rc.2-next-645ec5d6f-20220317',
  '18.0.0-rc.2-next-72a933d28-20220309',
  '18.0.0-rc.2-next-8fce11699-20220316',
  '18.0.0-rc.2-next-b9de50d2f-20220308',
  '18.0.0-rc.3-next-de516ca5a-20220321'
*/
npm view react versions
```

> 学习：
>
> 1. 使用 `npm view react version`查看react的最新正式版本(不包含alpha, beta, rc版本)
>
> 2. 使用 `npm view react versions`查看所有的react版本(包含alpha, beta, rc版本)
>
> 3. 版本号规则
>    主版本号.次版本号.修订号  例如：17.0.2
>
>    修改主版本号是做了大的功能性的改动; (很有可能写法与之前的不兼容)
>
>    修改次版本号是新增了新功能;
>    
>    修改修订号就是修复了一些bug;
>    
>    alpha: 内测版本；
>    
>    beta: 公测版本；
>    
>    rc(release candiate): 发布候选版本；



## 依赖配置

通常情况下，我们的项目会依赖一个或者多个外部的依赖包，根据依赖包的不同用途，可以将他们配置在下面的五个属性下：`dependencies`,  `devDependencies`, `peerDependencies`, `bundledDependencies`, `optionalDependencies` 。下面就来看看每个属性的含义。

#### dependencies

dependencies字段中声明的是项目的生产环境中所必须的依赖包。当使用 npm 或 yarn 安装npm包时，该npm包会被自动插入到此配置项中：

```json
npm install <PACKAGENAME>
yarn add <PACKAGENAME>
```

dependencies的值是一个对象，该对象的各个成员，分别由模块名和对应的版本要求组成，表示依赖的模块及其版本范围。

```json
"dependencies": { 
   "react": "^17.0.2", 
   "react-dom": "^17.0.2", 
   "react-scripts": "4.0.3",
},
```

这里每一项配置都是一个键值对（key-value）， key表示模块名称，value表示模块的版本号。版本号遵循**主版本号.次版本号.修订号**的格式规定：

- **固定版本：** 上面的react-scripts的版本4.0.3就是固定版本，安装时只安装这个指定的版本；
- **波浪号：** 比如~4.0.3，表示安装4.0.x的最新版本（不低于4.0.3），也就是说安装时不会改变主版本号和次版本号；
- **插入号：** 比如上面 react 的版本^17.0.2，表示安装17.x.x的最新版本（不低于17.0.2），也就是说安装时不会改变主版本号。**如果主版本号为0，那么插入号和波浪号的行为是一致的；**
- **latest**：安装最新的版本。
- **\***：安装所有的版本。（好像和latest一样的效果，都是安装最新版本）

需要注意，不要把测试或者过渡性的依赖放在dependencies，避免生产环境出现意外的问题。

> 学习：
>
> 1. 版本号的规则
>
>    - **固定版本：** 上面的react-scripts的版本4.0.3就是固定版本，安装时只安装这个指定的版本；
>    - **波浪号：** 比如~4.0.3，表示安装4.0.x的最新版本（不低于4.0.3），也就是说安装时不会改变主版本号和次版本号；
>    - **插入号：** 比如上面 react 的版本^17.0.2，表示安装17.x.x的最新版本（不低于17.0.2），也就是说安装时不会改变主版本号。如果主版本号为0，那么插入号和波浪号的行为是一致的；
>    - latest：安装最新的版本。
>
> 2. 使用以下命令将包安装在dependencies中：
>
>    `npm install <PACKAGENAME>`
>    `yarn add <PACKAGENAME>`



#### devDependencies

devDependencies中声明的是开发阶段需要的依赖包，如Webpack、Eslint、Babel等，用于辅助开发。它们不同于 dependencies，因为它们只需安装在开发设备上，而无需在生产环境中运行代码。**使用`npm install --production`命令只会安装dependencies，而不会安装devDependencies。**

因为打包上线时并不需要这些包，所以可以把这些依赖添加到 devDependencies 中，这些依赖依然会在本地指定 `npm install` 时被安装和管理，但是不会被安装到生产环境中。

当使用 npm 或 yarn 安装软件包时，指定以下参数后，新安装的npm包会被自动插入到此列表中：

```json
npm install --save-dev <PACKAGENAME>
yarn add --dev <PACKAGENAME>
"devDependencies": { 
  "autoprefixer": "^7.1.2", 
  "babel-core": "^6.22.1"
}
```

> 学习：
>
> 1. 使用以下命令把包安装在devDependencies中：
>
>    `npm install --save-dev <PACKAGENAME>`
>    `yarn add --dev <PACKAGENAME>`
>    
> 2. `npm install --production`命令只会安装dependencies，而不会安装devDependencies



#### peerDependencies

#####part1:

一般用在发布的代码库当中，表示需要宿主环境提供该配置下的模块依赖，与宿主环境息息相关。npm 3.x 版本之后不会自动安装该配置下的依赖模块，会告警提示。

```json
// 以 react-router (react 路由库) 的官方库为例，react-router 需要在 react 环境中使用
{
  "peerDependencies": {
    "react": ">=15"
  }
}
```

![image-20220323161313393](/Users/weitong/Library/Application Support/typora-user-images/image-20220323161313393.png)

这就是为什么我们用`npm install`的时候有时候看到很多WARN的原因，这不是错误信息，这是在提醒我们用的时候需要手动加上它的依赖。比如我们在用"react-router": "^6.2.2",的时候，需要同步手动安装"react": ">=16.8"的版本：

```json
"dependencies": {
    "react": "^17.0.2", //因为react-router需要，即使我这个项目不需要使用react，也需要自己安装上react
    "react-router": "^6.2.2",
  }
```



#####part2:

有些情况下，我们的项目和所依赖的模块，都会同时依赖另一个模块，但是所依赖的版本不一样。比如，我们的项目依赖A模块和B模块的1.0版，而A模块本身又依赖B模块的2.0版。大多数情况下，这不是问题，B模块的两个版本可以并存，同时运行。但是，有一种情况，会出现问题，就是这种依赖关系将暴露给用户(通过peerDependencies暴露给用户)。

最典型的场景就是插件，比如A模块是B模块的插件。用户安装的B模块是1.0版本，但是A插件只能和2.0版本的B模块一起使用。这时，用户要如果将1.0版本的B的实例传给A，就会出现问题。因此，需要一种机制，在模板安装的时候提醒用户，如果A和B一起安装，那么B必须是2.0模块。

peerDependencies字段就是用来供插件指定其所需要的依赖包的版本。

```
"name": "chai-as-promised",
"peerDependencies": { 
   "chai": "1.x"
}
```

上面代码指定在安装chai-as-promised模块时，主程序chai必须一起安装，而且chai的版本必须是1.x。如果项目指定的依赖是chai的2.0版本，就会报错。

需要注意，**从npm 3.0版开始，peerDependencies不再会默认安装了。**

> 学习：
>
> 1. peerDependencies解释了为什么npm install的时候有很多warn
> 2. 从npm3.0版本开始，peerDependencies不再会默认安装了，我们需要用npm install xxx手动安装插件的依赖



#### bundledDependencies

该配置下的模块通过 `npm pack` 会被打包到生成的模块包中，一般用于私密包引用，把文件配置到其他项目的依赖中。

**特别注意**： 

* 该配置是数组格式，包含需要被打包的包名。
* 这个字段数组中的值必须是在dependencies, devDependencies两个里面声明过的包才行。

例子：

```json
"name": "awesome-web-framework",
"version": "1.0.0",
"bundledDependencies": [
    'renderized', 'super-streams'
   ]
```

`npm pack`会将`renderized `和`super-streams`放入生成的包**awesome-web-framework-1.0.0.tgz**中，并且在`npm install awesome-web-framework-1.0.0.tgz`时，`renderized `和`super-streams`也会被一同安装。

>学习：
>
>1. bundledDependencies这个字段数组中的值必须是在dependencies, devDependencies两个里面声明过的包才行
>
>2. 使用`npm pack`将项目打包成tgz时，bundledDependencies会被打包进tgz中；



#### optionalDependencies

如果需要在找不到包或者安装包失败时，npm仍然能够继续运行，则可以将该包放在optionalDependencies对象中，optionalDependencies对象中的包会覆盖dependencies中同名的包，所以只需在一个地方进行设置即可。

需要注意，由于optionalDependencies中的依赖可能并没有安装成功，所以一定要做异常处理，否则当获取这个依赖时，如果获取不到就会报错。



#### engines

当我们维护一些旧项目时，可能对npm包的版本或者node版本有特殊要求，如果不满足条件就可能无法将项目跑起来。为了让项目开箱即用，可以在engines字段中说明具体的版本号：

```json
"engines": { 
  "node": ">=8.10.3 <12.13.0",  
  "npm": ">=6.9.0"
}
```

需要注意，**engines只是起一个说明的作用，即使用户安装的版本不符合要求，也不影响依赖包的安装。**

![image-20220324135604773](/Users/weitong/Library/Application Support/typora-user-images/image-20220324135604773.png)

> 学习：
>
> 我们可以在自己的项目中也配置上这个engines，用于提示新员工或则用户使用的时候使用正确的node版本。



# 脚本配置

#### scripts

scripts 是 package.json中内置的脚本入口，是key-value键值对配置，key为可运行的命令，可以通过 npm run 来执行命令。除了运行基本的scripts命令，还可以结合pre和post完成前置和后续操作。先来看一组scripts：

```shell
"scripts": {
  "dev": "node index.js",  
  "predev": "node beforeIndex.js", 
  "postdev": "node afterIndex.js"
}
```

这三个js文件中都有一句console：

```js
// index.js
console.log("scripts: index.js")
// beforeIndex.js
console.log("scripts: before index.js")
// afterIndex.js
console.log("scripts: after index.js")
```

当我们执行npm run dev命令时，输出结果如下：

```shell
scripts: before index.js
scripts: index.js
scripts: after index.js
```

可以看到，三个命令都执行了，执行顺序是predev→dev→postdev。如果scripts命令存在一定的先后关系，则可以使用这三个配置项，分别配置执行命令。

通过配置scripts属性，可以定义一些常见的操作命令：

```shell
"scripts": { 
  "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
  "start": "npm run dev", 
  "unit": "jest --config test/unit/jest.conf.js --coverage", 
  "test": "npm run unit", 
  "lint": "eslint --ext .js,.vue src test/unit", 
  "build": "node build/build.js"
}
```

这些脚本是命令行应用程序。可以通过调用 npm run XXX 或 yarn XXX 来运行它们，其中 XXX 是命令的名称。例如：npm run dev。我们可以为命令使用任何的名称，脚本也可以是任何操作。

使用好该字段可以大大的提升开发效率。

> 学习：
>
> script的执行顺序是predev→dev→postdev



#### config

config字段用来配置scripts运行时的配置参数，如下所示：

```shell
"config": { 
      "port": 3000
}
```

如果运行npm run start，则port字段会映射到`npm_package_config_port`环境变量中：

```js
console.log(process.env.npm_package_config_port) // 3000
```

用户可以通过`npm config set foo:port 3001` 命令来重写port的值。

> 学习：
>
> 在config中可以配置参数，然后我们可以使用`process.env.npm_package_config_参数名`的方式访问到这个变量。

## 描述信息

#### description

description字段用来描述这个项目包，它是一个字符串，可以让其他开发者在 npm 的搜索中发现我们的项目包。



#### keywords

keywords字段是一个字符串数组，表示这个项目包的关键词。和description一样，都是用来增加项目包的曝光率的。下面是eslint包的描述和关键词：

![Image](https://mmbiz.qpic.cn/mmbiz/zPh0erYjkib1aj8S2ry4VrRMhxibcZowqa6wm1maVexCAfwtKAoIPSZgCgJfccw3mPRnuH9ThV5BXb1G8oohIhMQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)



####author

author顾名思义就是作者，表示该项目包的作者。它有两种形式，一种是字符串格式：

```
"author": "CUGGZ <xxxxx@xx.com> (https://juejin.cn/user/3544481220801815)"
```

另一种是对象形式：

```
"author": {
  "name" : "CUGGZ",
  "email" : "xxxxx@xx.com",
  "url" : "https://juejin.cn/user/3544481220801815"
}
```



#### contributors

contributors表示该项目包的贡献者，和author不同的是，该字段是一个数组，包含所有的贡献者，它同样有两种写法：

```
"contributors": [
  "CUGGZ0 <xxxxx@xx.com> (https://juejin.cn/user/3544481220801815)",
  "CUGGZ1 <xxxxx@xx.com> (https://juejin.cn/user/3544481220801815)"
 ]
"contributors": [
  {
   "name" : "CUGGZ0",
   "email" : "xxxxx@xx.com",
   "url" : "https://juejin.cn/user/3544481220801815"
 },
  {
   "name" : "CUGGZ1",
   "email" : "xxxxx@xx.com",
   "url" : "https://juejin.cn/user/3544481220801815"
 }
 ]
```



####homepage

homepage就是项目的主页地址了，它是一个字符串。



####repository

repository表示代码的存放仓库地址，通常有两种书写形式。第一种是字符串形式：

```
"repository": "https://github.com/facebook/react.git"
```

除此之外，还可以显式地设置版本控制系统，这时就是对象的形式：

```
"repository": {
  "type": "git",
  "url": "https://github.com/facebook/react.git"
}
```

### 

####bugs

bugs表示项目提交问题的地址，该字段是一个对象，可以添加一个提交问题的地址和反馈的邮箱：

```
"bugs": { 
  "url" : "https://github.com/facebook/react/issues",
  "email" : "xxxxx@xx.com"
}
```

最常见的bugs就是Github中的issues页面，如上就是react的issues页面地址。