---
layout: _post
title: Webpack知识概览
date: 2020-08-07 19:48:00
tags:
	- Webpack
	- Node
categories:
	- Web前端
cover: https://cdn.jsdelivr.net/gh/aizener/my-imgs@master/blog/webpack.jpg
---

> Webpack是一个基于Node.js的前端构建工具。
> 前端构建工具就是把开发环境的代码转化成运行环境代码。一般来说，开发环境的代码是为了更好的阅读，而运行环境的代码则是为了能够更快地执行。因此开发环境和运行环境的代码形式也不相同。比如，开发环境的代码，要通过混淆压缩后才能放在线上运行，这样代码体积更小。

#### 功能

Webpack有以下几点功能：

- 代码压缩

  将HTML，CSS，JS等代码压缩打包成更小的体积，使得加载更快；

- 编译语法

  将浏览器目前不识别的CSS，JS等语法编译成浏览器可识别的；

- 处理模块化

  处理浏览器无法识别的JS或CSS无法识别的模块话语法。

#### 安装

- npm

  `npm install webpack webpack-cli --save-dev`

- yarn

  `yarn add webpack webpack-cli --dev`

#### 概念

从 webpack v4.0.0 开始，可以不用引入一个配置文件。然而，webpack 仍然还是[高度可配置的](https://www.webpackjs.com/configuration)。在开始前你需要先理解四个**核心概念**：

##### 入口（entry）

入口文件就是Webpack需要打包的js文件，因为Webpack只能识别js文件，所以想要打包其他类型的文件，需要添加模块或插件，这个后面说到，先来看个小案例：

```js
class Hello {
	constructor () {
		console.log('hello.')
	}
}
new Hello()
```

然后，安装webpack，执行语句：`webpack index.js`，会发现webpack给我们生成了一个**dist**目录，这个目录里面就有webpack帮我们打包之后的文件，打开文件后会发现webpack帮我们把代码压缩了。

但是，在执行这个命令时可能会有黄色的警告，这是因为我们没有声明**模式**。

##### 出口（output）

我们新建一个`webpack.config.js`进行配置：

```js
const { resolve } = require('path')

module.exports = {
	mode: 'development',	// 模式
	entry: './index.js',	// 入口文件
	output: {				// 出口
		path: resolve(__dirname, './dist'),	// 打包的目录，必须用绝对路径
		filename: 'index.js'	// 打包生成的文件名字
	}
}
```

执行`webpack`，便会看到打包了一个dist目录，并且里面有一个index.js文件。这里和上面不一样，是因为webpack本身就有默认配置，当我们新建了一个`webpack.config.js`时，webpack会找到这个文件并加载配置来执行。

如果这个文件不是`webpack.config.js`的话，可以通过显示的执行`webpack --config ./webpack-demo.config.js`来加载配置。

##### 插件（plugins）

上面，我们打包了一个js文件，但是如果要引用到html文件的话，还需要在html文件里面手动引入打包后的js文件，这样很不方便也不合适。所以，Webpack提供了**plugins**来提供给我们使用。

```js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	mode: 'development',
	entry: resolve(__dirname, './index.js'),
	output: {
		path: resolve(__dirname, './dist'),
		filename: 'index.js'
	},
	plugins: [
		new HtmlWebpackPlugin({
			title: 'demo',
			template: './index.html'
		})
	]
}
```

通过`html-webpack-plugin`我们就可以轻松的也把html文件也打包到dist目录，并且html文件还引入了js文件。

另外，这里再介绍一个`clean-webpack-plugin`，这个插件用来每次打包时先清空dist目录。这个有什么用呢？目前是看不到效果，但是当我们用`[hash]`这样的占位符生成文件名称时，那么每一次打包时以前的文件就还会存在。

##### 模块/加载器（modules/loader）

基于上面情况，如果现在我们要引入CSS模块的话，就需要使用`loader`了，通过加载器，我们就可以让webpack支持对CSS、Less等的打包功能。补充：[关于Babel](https://www.babeljs.cn/)。

```js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	mode: 'development',
	entry: resolve(__dirname, './index.js'),
	output: {
		path: resolve(__dirname, './dist'),
		filename: 'index.js'
	},
	module: {
		rules: [
			{
				test: /.css$/,
				use: ['style-loader', 'css-loader']
			}
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			title: 'demo',
			template: './index.html'
		})
	]
}
```

上面的`module`就是模块，有一个属性`rules`包含一系列`loader`加载时的要求。每一个rule就对应着一类文件，其中：

- test： 正则表达式，要匹配的文件
- use：需要使用的loader，使用顺序是从右往左，像上面的`css-loader`和`style-loader`是不能交换顺序的，因为需要先解析css模块，再生成style标签插入解析的css。

再来看一个例子，把ES6语法编译为兼容性更好的ES5语法，webpack里面使用babel，如下：

```js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	mode: 'development',
	entry: resolve(__dirname, './index.js'),
	output: {
		path: resolve(__dirname, './dist'),
		filename: 'index.js'
	},
	module: {
		rules: [
			{
				test: /.css$/,
				use: ['style-loader', 'css-loader']
			},
			{
				test: /.js$/,
				exclude: /node_modules/,
				use: {
					loader: 'babel-loader',
					options: {
						presets: ['@babel/preset-env']
					}
				}
			}
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			title: 'demo',
			template: './index.html'
		})
	]
}
```

上面用到了一个`exclude`，也是匹配文件的，但却是把匹配的文件排除在这个loader处理范围之外，优先级比`test`高，`use`这里因为只有一个而且需要详细配置，使用了对象的方式，`loader`就是需要的加载器，`options`就是这个加载器的一些配置信息。

##### 模式（mode）

webpack的模式，分为`development`和`production`，可以通过`mode`设置，也可以通过执行命令时设置：`webpack --mode=development`。

### 自定义Loader和Plugin

除了webpack自带以外的loader和plugin以外，还有大部分自定义的loader和plugin供我们使用，那么怎么自定义一个简单的Loader或Plugin呢？

在此之前，我们先配置一下`resolveLoader`，这个参数表示webpack从哪里来加载对应的loader：

```js
resolveLoader: {
    modules: ['node_modules', './']
},
```

这样，webpack就会先从`node_modules`下面找对应`loader`，找不到又会根据当前目录来找。

##### 自定义Loader

loader其实就是导出的一个函数，这个函数接受一个`source`参数，就是入口文件的内容，这个函数应该避免用箭筒函数的方式书写，因为除了这个参数，方法的`this`对象还有很多Api可以使用。

- 同步方式：

  ```js
  module.exports = function (source) {
      console.log(source)
      // ...对source进行各种操作
      return source // 返回必须是二进制或者字符串
      // this.callback(null, source)
  }
  ```

  同步返回方式有两种，一个是直接返回**字符串或者二进制对象**，另一种是通过**this来调用callback**，这个回调函数第一个参数是错误信息，第二个参数就是返回的**字符串或者二进制对象**。

- 异步方式：

  ```js
  module.exports = function (source) {
      console.log(source)
      // ...对source进行各种操作
      const asyncCallback = this.async()
      setTimeout(() => {
          asyncCallback(null, source)
      }, 1e3)
  }
  ```

  异步方式和同步方式很接近，只是需要调用一下`this.async()`，然后和`this.callback`的执行方式类似。

##### 自定义Plugin

自定义的`Plugin`其实就是定义的一个类，通过引用到webpack配置文件示例并引用时，在webpack打包的过程中会执行`PluginClass`的`apply`方法，如下：

```js
module.exports = class DemoPlugin {
	constructor (data) {
		 console.log(data)
	}
	apply(compiler) {
		console.log(compiler)
	}
}
```

上面就是最最简单不过的一个Plugin示例：

- data：构造函数传过来的参数；

- apply：由webpack执行的时候执行，有一个参数compiler，这个参数很复杂：

  `Compiler` 模块是 webpack 的支柱引擎，它通过 [CLI](https://www.webpackjs.com/api/cli) 或 [Node API](https://www.webpackjs.com/api/node) 传递的所有选项，创建出一个 compilation 实例。它扩展(extend)自 `Tapable` 类，以便注册和调用插件。大多数面向用户的插件首，会先在 `Compiler` 上注册。

  具体看[官网教程](https://www.webpackjs.com/api/compiler-hooks/)

### DevServe

webpack除了打包文件的功能以外，还提供了开发中环境供我们开使用，方便了我们调式以及开发，使得能够用于快速开发应用程序。

```js
devServer: {
  contentBase: path.join(__dirname, "dist"),
  compress: true,
  port: 9000,
  host: 'localhost'
}
```

这是一个简单的例子：

- contentBase: 服务器启动的所在目录，默认是根目录；
- compress：一切服务都起用gzi压缩；
- host：启动服务的主机名；
- port： 启动服务的端口。

[DevServer详细资料](https://github.com/webpack/webpack-dev-server)

### 更多配置

webpack足够的强大，这里说的仅仅是九牛一毛，更多配置请看[官网配置](https://www.webpackjs.com/configuration/)。