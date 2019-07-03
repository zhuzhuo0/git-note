## React 【学习总结】

版本：16.3.2

16.3.2 之后的称为 React Fiber

引入方式

1. 通过script标签的方式引入
2. 通过脚手架工具来编码

官方脚手架

create-react-app

```
# 安装脚手架工具
npm install -g create-react-app
create-react-app my-app

# 一次性安装
npx create-react-app my-app
```

`Manifest.json`和`registerServiceWork.js`是和PWA相关的配置文件和方法

运用到 `JSX` 语法，需要组建中引入`react`  示例  `import React from 'react'`

样式 `class` 替换为 `className` 

`lable` 标签中的 `for` 属性替换为 `htmlFor` 

`render` 中的内容必须要一个根结点包裹 可以用 `<Fragment>内容</Fragment>` 包裹

```
# 改变组件状态
this.setState({
	key:value
})
# 修改为
this.setState((prevState)=>({key:value}))
```

可以与 `vue`，`angular` 共存

`react`只管理挂载在根结点上的dom树 `<div id="root"></div>`

声明式开发，组件化，单向数据流（修改数据通过父节点），视图层框架（所以需要引入redux数据层框架）， 可以与其他框架共存（只在所挂载根结点的树上有效）， 函数式编程（测试）

`PropTypes` 传入值类型校验  `DefaultProps` 默认传入值

当组件中的 `state` 和 `props` 值发生改变就会触发 `render` 函数重新渲染页面 

当父组件的 `render` 被重新执行，子组件的 `render` 也会被重新执行

#### 虚拟Dom原理

1. state数据
2. JSX模板
3. 数据 + 模板 结合，生成真实的DOM，来显示
4. state 发生改变
5. 数据 + 模板 结合，生成真实的DOM，替换原始的DOM

缺陷

1. 局部变化造成，整个dom树重新生成并替换，消耗性能

修改

1. state数据
2. JSX模板
3. 数据 + 模板 结合，生成真实的DOM，来显示
4. state 发生改变
5. 数据 + 模板 结合，生成真实的DOM，并不直接替换原始的Dom
6. 新的dom（DocumentFragment 文档碎片，存在内存中）和原始的dom 做比较，找差异
7. 找出input发生的变化
8. 只用新的dom中的input元素，替换掉老的dom中的input元素

缺陷

1. 性能提升并不明显，虽然不需要完全替换原始dom，但是需要做dom对比

React

1. state数据

2. JSX模板

3. 数据 + 模板 结合，生成真实的DOM，来显示 

   `<div id="abc"><span>hello word</span></div>`

4. 生成虚拟DOM（虚拟dom就是一个js对象，用它来描述真实dom）

   `['div',{id:'abc'},['span',{},'hello world']]`

   用JS生成JS对象的消耗成本是很小的，但是用JS生成DOM对象的代价极高

5. state发生变化

6. 数据 + 模板生成新的虚拟dom（极大的提升了性能）

   `['div',{id:'abc'},['span',{},'bye bye']]`

7. 比较原始dom和新的虚拟dom的区别，找到区别是span中内容（之前是DOM和DOM直接做对比，现在是js对象之间的比较，极大的提升了性能）

8. 直接操作dom，改变span中的内容

总结：减少DOM对象的创建和对比，通过js对象的创建和对比，来提升性能

#### 深入了解虚拟DOM

实际是：

React

1. state数据

2. JSX模板

3. 数据 + 模板  生成虚拟DOM（这一步实际上是损耗性能的，虚拟dom就是一个js对象，用它来描述真实dom）

   `['div',{id:'abc'},['span',{},'hello world']]`

   理由：用JS生成JS对象的消耗成本是很小的，但是用JS生成DOM对象的代价极高

4. 用虚拟dom的结构生成真实的DOM，来显示 

   `<div id="abc"><span>hello word</span></div>`

5. state发生变化

6. 数据 + 模板生成新的虚拟dom（极大的提升了性能）

   `['div',{id:'abc'},['span',{},'bye bye']]`

7. 比较原始dom和新的虚拟dom的区别，找到区别是span中内容（之前是DOM和DOM直接做对比，现在是js对象之间的比较，极大的提升了性能）

8. 直接操作dom，改变span中的内容

```
// JSX => React.createElement => 虚拟DOM（JS对象）=> 真实的DOM
return <div><span>item</span></div>
# 等同于
return React.createElement('div',{},React.createElement('span',{},'item'));
```

虚拟DOM的好处

1. 性能提升（DOM比对改为JS对象比对）
2. 它使得跨端应用得以实现，React Native

#### 虚拟DOM中的Diff算法

state改变和props发生改变（props发生改变根本上来说就是父组件state发生改变），执行时机是setState()调用

setState是异步的

比方说更改频发，合并操作

同级比较：减少对比消耗，比较速度较快。   如果同层比较发现不同之后，之后的子节点就会直接废弃，重新创建

key值，提升比较性能。 但是要保证同级比较对应的key值相同，不要用index作为key，key值要保证稳定

#### React 性能优化

1. constructor中初始化数据状态和事件this绑定，保证只会执行一次，而且可以避免组件无谓渲染
2. setState通过异步的方式，优化性能
3. 虚拟dom，同层比对，for循环键值对影的方式，优化性能
4. 通过shouldComponentUpdate，减少无谓渲染，优化性能

#### React 生命周期函数

生命周期函数是指在某一时刻组件会自动调用的函数

场景一：

父组件的render函数如果执行，子组建的render函数也会被执行，如果子组建中传入的props没有被更新，这样重复render会造成性能损耗

解决方法：

```
shouldComponentUpdate(nextProps,nextState){
	// 对内容进行比较，判断是否需要更新
	if(nextProps.content !== this.props.content){
		return true;
	}else{
		return false;
	}
}
```

场景二：

初始化异步请求放在componentDidMount中（找到执行一次的生命周期函数）

#### React的CSS过渡动画

```
# CSS 动画 通过transition或者animate方式实现动画效果
.show{
	opacity:1;
	transition:all 1s ease-in;
}
.hide{
	animation:hide-item 2s ease-in forwards; // forwards 保存动画效果最后一帧
}
@keyframes hide-item{
	0%{
		opacity:1;
		color:red;
	}
	50%{
		opacity:0.5;
		color:green;
	}
	100%{
		opacity:0;
		color:blue;
	}
}
```

#### Redux = Reducer + Flux

redux的工作流程

reducer接收state但是绝对不能修改传入的state

#### Redux设计和使用三个基本原则

1. store是唯一的

   整个项目只有一个store作为公共存储空间

2. 只有store能改变自己的内容

   reducer只是拿到之前的数据，然后返回新的数据，store根据新的数据对自己进行更新

3. reducer必须是纯函数

   纯函数：给定固定的输入，就一定会有固定的输出，而且不会有任何的副作用；只依赖传入的参数

   副作用：比如对参数的修改，或者赋值外部变量

#### store api

1. createStore：创建store
2. store.dispatch：派发改变
3. store.getState：获取store内容
4. store.subscribe：订阅store数据改变，并做相应处理

#### UI组件与容器组件的拆分

组件内部包含UI渲染和逻辑处理，即可拆分成UI组件（负责页面渲染）和容器组件（负责页面逻辑）

#### 无状态组件

```
// 普通组件
import React, {Component} from 'react';

class TodoList extends Component {
	render(){
		return (
			// JSX模板
		)
	}
}

# 当一个普通组件中只有render函数时，可将普通组件定义为无状态组件，如下
// 无状态组件
const TodoList = (props) => (
	// JSX模板
)
```

无状态组件优势

性能更好，只是定义的一个函数，不用通过继承类实现的，没有各种生命周期函数，所以不需要执行默认的生命周期钩子，节约性能开销；一般UI组件通过无状态组件的方式定义

#### Redux中发送数据，获取异步数据

1. componentDidMount()中做异步请求
2. 通过redux-thunk实现异步请求统一管理

#### Redux中间件原理

action和store中间

对dispatch方法的封装：原始方式，传递对象；封装之后，根据传入的参数，做不同处理

例如：redux-logger，redux-thunk（将异步操作放到action中进行），redux-saga（将异步操作放到单独文件中进行处理）

#### Redux-saga中间件的使用

redux-saga将异步处理单独放在一个文件之中，适合处理项目规模较大，复杂度较高的情况，同时提供的api较多，学习成本较高

redux-thunk基本没有什么额外的api，只是对action进行了扩展，使其能够通过函数的方式传递action并处理完异步请求之后，再发送新的action，较为简单，但是写在action中，项目规模大了之后，不利于节藕

#### React-Redux的使用

connect返回的结构是容器组件，对UI组件进行包装