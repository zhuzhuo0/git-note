## React测试教程

#### React特别时候单元测试

1. 组件化（独立单元，互不影响）
2. Functional Component （纯函数组件）
3. 单向数据流（不让组件随便修改传入的数据，利于测试，指定输入，确定输出）

#### 通用测试框架 - Jest

特定语法编写测试用例，并且能够运行

1. 通用测试框架
2. 支持多平台，通过workers运行速度极快
3. 内置代码覆盖率测试
4. 为React提供了一些特殊的测试方法

#### 断言库

判断一个值是否对应相应的结果

#### React测试工具

将组件挂载到用例上进行测试

1. React官方测试工具 - ReactTestUtils （使用复杂）
2. Airbnb基于官方的封装 - Enzyme

#### Enzyme 优点

1. 简单 易懂
2. 类似Jquery链式写法

#### 两种测试方法

Shallow Rendering 隔离，不渲染子组件，对与简单的functional Component使用起来很方便

Dom Rendering 渲染组件，子组件，还可以调用生命周期函数，缺点：速度比较慢

#### 简单测试用例

```
# 安装
yarn add enzyme enzyme-adapter-react16 --save-dev

# 新建setupTest.js (作用：链接craete-react-app，脚手架自动加载)
import {configure} from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'

configure({adapter:new Adapter()})
注：在运行test的时候，会自动加载配置文件用于连接

# Functional Component ut
# demo.js
import React from 'react'

const Demo = ({income,outcome}) => (
	<div>
		<p className='income'>{income}</p>
		<p className='outcome'>{outcome}</p>
	</div>
)

export default Demo

# demo.test.js
import React from 'react'
import {shallow} from 'enzyme'
import Demo from './demo'

const props = {
	income:1000,
	outcome:2000
}

describe('test Demo Component',()=>{
	it('component should render correct income and outcome number',()=>{
		const wrapper = shallow(<Demo {...props} />)
		expect(wrapper.find('.income').text()*1).toEqual(1000)
		expect(wrapper.find('.outcome').text()*1).toEqual(2000)
	})
})

```

#### 单元测试用例分析

#### Snapshot testing

1. jest为React测试提供的特性
2. 为价格类表添加snapshot