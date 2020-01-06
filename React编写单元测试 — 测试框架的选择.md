## React编写单元测试 — 测试框架的选择

#### Jest

Jest 是 Facebook 开发的一款 JavaScript 测试**框架**。在 Facebook 内部广泛用来测试各种 JavaScript 代码。其官网上主要列出了以下几个特点：

- 轻松上手
  - 使用 [`create-react-app`](https://facebook.github.io/react/blog/2016/07/22/create-apps-with-no-configuration.html) 或是 [`react-native init`](http://facebook.github.io/react-native/docs/getting-started.html) 创建的项目已经默认集成了 Jest
  - 现有项目，只需创建一个名为 `__test__` 的目录，然后在该目录中创建以 `.spec.js` 或 `.test.js` 结尾的文件即可
- 内置强大的断言与 mock 功能
- 内置测试覆盖率统计功能
- 内置 Snapshot 机制

虽然 Jest 官网介绍中多次 React，但实际上 Jest 并不是和 React 绑定的。你可以使用它测试任何 JavaScript 项目。

#### Enzyme

由 Airbnb 出品。官方文档中给自己的定义是：

> Enzyme is a JavaScript Testing utility for React that makes it easier to assert, manipulate, and traverse your React Components’ output.

从这里可以看出两点信息：

1. Enzyme 的定位是一个**工具库**
2. Enzyme 的出现是为了让我们**更方便**的**遍历、操作 React 组件输出的内容**

#### Mocha

Mocha 是另一个 JavaScript 测试**框架**。与 Jest 不同的是，它自身只提供作为一个测试框架最核心的功能。而其它增强功能，如丰富的断言语法、mock、测试覆盖率统计等功能则是通过各种 [Add-ons](https://github.com/mochajs/mocha/wiki#add-ons) 提供的。与各种 Add-ons 搭配在一起形成了各种各样的“套餐”。而最常见的组合应该就是如下这样的搭配：

- [Chai](http://www.chaijs.com/) 负责断言
- [Sinon.js](http://sinonjs.org/) 负责 mock
- [Istanbul](https://github.com/gotwarlost/istanbul) 负责统计测试覆盖率

#### React-test-renderer

在说 `react-test-renderer` 之前，让我们先聊聊什么是 `renderer`。React 最早是被用来开发网页的，所以早期的 React 库中还包含了大量和 DOM 相关的逻辑。后来 React 的设计思想慢慢被迁移到其它场景，最被人们熟知的莫过于 [React Native](http://facebook.github.io/react-native/) 了。为了灵活性和扩展性，React 的代码被分拆为 React 核心代码与各种 `renderer`。React 自带了 3 个 renderer，前两个是大家常见的：

- [react-dom](https://github.com/facebook/react/tree/master/packages/react-dom) 负责将组建渲染到浏览器页面中。
- [react-native-renderer](https://github.com/facebook/react/tree/master/packages/react-native-renderer) 负责将组件渲染成原生场景中的各种 “View”。

而今天提到的 [react-test-renderer](https://github.com/facebook/react/tree/master/packages/react-test-renderer) 则负责将组件输出成 JSON 对象以方便我们遍历、断言或是进行 snapshot 测试。

备注：[这里](https://github.com/chentsulin/awesome-react-renderer)有一份各种各样的 renderer 列表。

#### React-Dom/test-utils

首先从名称可以看出这个库是包含在 `react-dom` 中的。所以它只是 `react-dom` 的辅助测试工具。在 React 文档站中它的介绍页上用的标题却只有 “Test Utilities” 两个单词，很容易让人产生误解。该库中的方法主要作用是帮我们遍历 ReactDOM 生成的 DOM 树，方便我们编写断言。**注意：使用该库时必须提供一个 DOM 环境。**当然这个 DOM 环境可以是 [jsdom](https://github.com/jsdom/jsdom) 这种模拟环境。(友情提示：Jest 默认的执行环境就是 jsdom)

读到这你可能会问，`react-test-renderer` 和 `react-dom/test-utils` 两者看起来还是很相似。何时该选择哪一个库呢？

- 如果需要测试事件（如 click, change, blur 等），那么使用 `react-dom/test-utils`
- 其它时候使用更简单、灵活的 `react-test-renderer`



通过上文的描述，我们发现这些库可以分为两大类。

- 通用的基础设施：test runner、断言、mock、测试覆盖率统计均包含在这个范畴里。这些功能与测试什么项目并没有太大关系。Jest、Mocha以及它的小伙伴们都属于这一类。
- React 测试辅助工具库：Enzyme 以及 `react-test-renderer` 和 `react-dom/test-utils` 兄弟俩属于这一类。事实上，Enzyme 底层也是调用了这兄弟俩。并在之上做了大量接口封装。

基于以下的考虑，我们最终选择了 Jest + `react-test-renderer` 和 `react-dom/test-utils` 的组合。

- Jest 的零配置起步对新手非常友好。可以让我们把更多的精力放到如何写好测试，而不是如何用 Mocha 的各种 `add-ons` 搭建环境上。
- Enzyme 固然用起来 API 会更简单。但大量的封装对我们来说并不透明。而使用`react-test-renderer` 和 `react-dom/test-utils` 虽然在 API 上会略微繁琐，但是可以让我们更好的理解 React 的工作原理。