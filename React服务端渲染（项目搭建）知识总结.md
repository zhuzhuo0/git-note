## React服务端渲染（项目搭建）知识总结

UglifyJsPlugin

用来压缩JS文件

extract-text-webpack-plugin

抽离CSS样式

Css-loader：加载.css文件

style-loader：使用<style>将css-loader内部样式注入到我们的html页面

**css-loader options**

- `alias`: 解析别名
- `importLoader(@import)`
- `Minimize`: `true or false`,是否开启`css`代码压缩，比如压缩空格不换行。
- modules：是否开启css-modules



style-loader分类

style-loader:配合css-loader使用，以<style></style>形式在html页面中插入css代码

style-loader/url： 以link标签形式向html页面中插入代码，采用这种方式需要将css-loader变为file-loader,但这种方式不推荐，因为如果在一个js文件中引入多个css文件会生成多个link标签，而html每个link标签都会发送一次网络请求，所以这种方式并不建议。

style-loader/useable: 采取这种方式使用处理css，会有use( )和unuse（）l两种方法，use（）开启引入样式，unuse（)不适用样式。

options选项

-attrs: 添加自定义 attrs 到 style 标签
- insertAt:插入位置
- insertInto: 插入到指定dom
- singleton:类型为布尔值，多个样式是否只生成一个<style></style>标签。

transform:根据给定逻辑在css插入html前选择指定样式（具体可参考下面实例）



```
[
	// 增加对scss文件的支持
	test:/\.scss/,
	// scss文件的处理顺序为sass-loader，css-loader，style-loader
	use:['style-loader','css-loader','sass-loader']
]

1. 通过sass-loader将scss源码转成css代码，再将css代码交给css-loader处理
2. css-loader会找出css代码中的@import和url这样的导入语句，告诉webpack依赖这些资源，同时支持CSS Module、压缩css等功能，处理完后再将结果交给style-loader处理
3. style-loader会将CSS代码转成字符串后，注入javascript代码中，通过javascript向DOM增加样式，如果我们想将CSS代码提取到一个单独的文件中，而不是javascript混在一起，则可以用ExtractTextPlugin
```



file-loader

可以将javascript和css中导入图片的语句替换成正确的地址，同时将文件输出到对应的位置

url-loader

可以将文件的内容经过base64编码后注入javascript或者css中

一般两者配合，利用url-loader将页面中用到的小图片资源注入代码中，减少加载次数，到的文件输出到指定的目录内

javascript source map 

Source map就是一个信息文件，里面存储着位置信息，也就说，转换后的代码的每一个位置，所对应的转换前的位置，有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码

在代码结尾附加上map文件地址，类似`//@ sourceMappingURL=/path/to/file.js.map`

Source map的格式

```
打开Source map文件，它大概是这个样子：
{
　　　　version : 3,
　　　　file: "out.js",
　　　　sourceRoot : "",
　　　　sources: ["foo.js", "bar.js"],
　　　　names: ["src", "maps", "are", "fun"],
　　　　mappings: "AAgBC,SAAQ,CAAEA"
　　}
　　
整个文件就是一个JavaScript对象，可以被解释器读取。它主要有以下几个属性：

　　- version：Source map的版本，目前为3。

　　- file：转换后的文件名。

　　- sourceRoot：转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。

　　- sources：转换前的文件。该项是一个数组，表示可能存在多个文件合并。

　　- names：转换前的所有变量名和属性名。

　　- mappings：记录位置信息的字符串，下文详细介绍。
```

mappings属性

两个文件的各个位置是如何一一对应的。

关键就是map文件的mappings属性。这是一个很长的字符串，它分成三层。

> 　　第一层是**行对应**，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。
>
> 　　第二层是**位置对应**，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。
>
> 　　第三层是**位置转换**，以[VLQ编码](http://en.wikipedia.org/wiki/Variable-length_quantity)表示，代表该位置对应的转换前的源码位置。

举例来说，假定mappings属性的内容如下：

> 　　mappings:"AAAAA,BBBBB;CCCCC"

就表示，转换后的源码分成两行，第一行有两个位置，第二行有一个位置。

位置对应的原理

每个位置使用五位，表示五个字段。

从左边算起，

> 　　- 第一位，表示这个位置在（转换后的代码的）的第几列。
>
> 　　- 第二位，表示这个位置属于sources属性中的哪一个文件。
>
> 　　- 第三位，表示这个位置属于转换前代码的第几行。
>
> 　　- 第四位，表示这个位置属于转换前代码的第几列。
>
> 　　- 第五位，表示这个位置属于names属性中的哪一个变量。

有几点需要说明。首先，所有的值都是以0作为基数的。其次，第五位不是必需的，如果该位置没有对应names属性中的变量，可以省略第五位。再次，每一位都采用VLQ编码表示；由于VLQ编码是变长的，所以每一位可以由多个字符构成。

如果某个位置是AAAAA，由于A在VLQ编码中表示0，因此这个位置的五个位实际上都是0。它的意思是，该位置在转换后代码的第0列，对应sources属性中第0个文件，属于转换前代码的第0行第0列，对应names属性中的第0个变量。

Context

Webpack在寻找相对路径的文件时会以context为根目录，context默认为执行启动Webpack时所在的当前工作目录

context必须是一个绝对路径的字符串，entry的路径及其依赖的路径可能采用相对于context的路径来描述

#### 构建服务端渲染过程问题汇总

问题：

Error with task : Element type is invalid: expected a string (for built-in components) or a class/function (for composite components) but got: undefined. You likely forgot to export your component from the file it’s defined in.
碰到以上问题说明，模块导出引入部分出现问题，仔细将项目中的文件引用导出部分仔细检查，常见的是，导出的时候不是默认导出，但是引入却按照默认导入的方式导入

前后端分离的方式和后端渲染的方式采用的路由组件不同，具体查看react-router-dom文档，分离：BrowserRouter  SSR：StaticRouter

服务端路由不同于客户端，它是无状态的。React提供了一个无状态的组件[StaticRouter](https://link.juejin.im/?target=https%3A%2F%2Freacttraining.com%2Freact-router%2Fweb%2Fguides%2Fserver-rendering)，向`StaticRouter`传递url，调用`ReactDOMServer.renderToString()`就能匹配到路由视图

`StaticRouter`提供了一个props用来传递上下文对象`context`，在渲染路由组件时通过`staticContext`获取并设置状态码，服务端渲染时通过状态码判断做响应处理。如果服务端路由渲染时发生了重定向，通过`context`自动添加上与重定向相关信息的属性

Head管理：

通过`react-helmet`插件实现对head管理

通过setHead将head属性添加到组件上，以便于在`server.js` 中对数据进行处理

#### 单独打包第三方模块代码

`app.xxx.js`包含了第三方模块和业务代码，业务代码会随时变化，而第三方模块在一定的时间内基本不变，除非你对目前使用的框架或库进行升级。`app.xxx.js`中的xxx使用`chunkhash`命名，`chunkhash`表示chunk内容的hash，第三方模块的chunk不会变化，我们将其分离出来，便于浏览器缓存

为了提取第三方模块，需要使用webpack自带的[CommonsChunkPlugin](https://link.juejin.im/?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Fcommons-chunk-plugin)插件，同时为了更好的缓存我们将webpack引导模块提取到一个单独的文件中

#### webpack、manifest、runtime

# manifest

在使用 webpack 构建的典型应用程序或站点中，有三种主要的代码类型：

1. 你或你的团队编写的源码。
2. 你的源码会依赖的任何第三方的 library 或 "vendor" 代码。
3. webpack 的 runtime 和 **manifest**，管理所有模块的交互。

本文将重点介绍这三个部分中的最后部分，runtime 和 manifest，特别是 manifest。

## runtime 

runtime，以及伴随的 manifest 数据，主要是指：在浏览器运行过程中，webpack 用来连接模块化应用程序所需的所有代码。它包含：在模块交互时，连接模块所需的加载和解析逻辑。包括：已经加载到浏览器中的连接模块逻辑，以及尚未加载模块的延迟加载逻辑。

## manifest 

在你的应用程序中，形如 `index.html` 文件、一些 bundle 和各种资源，都必须以某种方式加载和链接到应用程序，一旦被加载到浏览器中。在经过打包、压缩、为延迟加载而拆分为细小的 chunk 这些 webpack [`优化`](https://webpack.docschina.org/configuration/optimization/) 之后，你精心安排的 `/src` 目录的文件结构都已经不再存在。所以 webpack 如何管理所有所需模块之间的交互呢？这就是 manifest 数据用途的由来……

当 compiler 开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "manifest"，当完成打包并发送到浏览器时，runtime 会通过 manifest 来解析和加载模块。无论你选择哪种 [模块语法](https://webpack.docschina.org/api/module-methods)，那些 `import` 或 `require` 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够检索这些标识符，找出每个标识符背后对应的模块。

解释：

其实这里，webpack帮我们定义了一个**webpack_require**的加载模块的方法，而**mainfest模块数据集合就是对应着bundle.js第三行的 installedModules** 。每当我们在main.js入口文件引入一模块，installModules就会发生变化，当我们页面点击跳转，加载对应模块就是通过__webpack_require__方法在installModules中找对应模块信息，进行加载。

**那这个跟缓存有什么关系呢？**
1、我们知道浏览器加载js文件，如果文件名是一致的会优先从本地缓存拿，如果没有才向服务器这边请求。这个是时候，我们可以知道，如果是业务代码，比如我优化了一个功能，更改了个bug，如果文件名没有变化，请求时，浏览器用了缓存数据，则我们的新代码没被用上，因为浏览器用了旧代码文件。**所以，output的选项中的filename，就不在是bundle.js ,而对应着bungdle[chunkhash].js，因为每次打包时，chunkhash都不同，所以打出来的文件名居不同，浏览器就不会直接用缓存数据了，而是请求新文件**

2、实际上，我们发现只有我们业务代码经常变化，而第三方依赖库是很少变化的，比如你使用了vue、vue-router、或者react等第三方库，这些是不会变化，那我们希望浏览器如果有的话，就不用再请求。但是，我们打包都整合到一个bundle[chunkhash].js上了，**所以，我们需要借助webpack.optimize.CommonsChunkPlugin来帮我们分离代码，将mainfest、第三方库包，业务代码独立成三个文件**

实际上，mainfest也会发生变化，它的变化在于你是否增加了模块，比如新引入一个js文件之类，mainfest也会变化。明白了mainfest，我们就可以更好的处理我们的缓存机制了

插件版本问题：

React-router-dom. 必须是4.x

react-redux.必须是5.x