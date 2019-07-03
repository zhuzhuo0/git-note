## React 实战【简书】

#### 创建项目

```bash
# 全局安装脚手架
yarn global add create-react-app
# 创建项目文件
create-react-app react-jianshu
# 进入文件夹
cd react-jiashu
# 启动项目
yarn start
```

#### 样式组件化

styled-components 第三方模块

```
# 全局样式 style.js
import {injectGlobal} from 'styled-components'

injectGlobal`
	body{
		margin:0;
		padding:0;
		font-family:sans-serif;
	}
`
```

```jsx
# 局部样式。实质通过styled-components创建带样式的组件，实现样式，（个人感觉很麻烦）
import styled from 'styled-components';

// 导出带样式的组件，供父组件调用
export const HeaderWrapper = styled.div`
	height:58px;
	border-bottom:1px solid #f0f0f0;
`;

// 其他组件...
// 例如：logo图片
import logoPic from '../../static/logo.png';
import styled from 'styled-components';

// 设置logo样式组件
export const Logo = styled.a.attrs({
	href:'/'
})`
	background:url(${logoPic});
	width:100px;
	height:56px;
`
```

#### 将reducer拆分为小的reducer

#### Immutable.js

不可改变的对象

```
yarn add immutable

# 使用 reducer.js
import {fromJS} from 'immutable'

// 将state转为immutable对象
const defaultState = fromJS({
	focused:false
})

// immutable方式
export default (state=defaultState,action)=>{
	if(action.type==='DEMO_TYEP'){
		// 之前的方式
		return {
			focused:true
		}
		// immutable方式
		// immutable对象的set方法，会结合之前immutable对象的值和设置的值，返回一个全新的对象
		return state.set('focused',true)
	}
}

// 取值
state.header.get('字段名')
```

#### React-router

```
yarn add react-router-dom

# 动态路由
/detail/:id
# 获取id
this.props.match.params.id
```

#### PureComponent

与Component区别，内置实现shouldComponentUpdate钩子，通过判断组件相关的数据是否改变控制是否渲染，而不是只要store发生改变就重新渲染组件，优化性能。

注意：PureComponent最好和immutable.js库配合使用，否则可能会出现问题，偏底层

#### 优化

1. 代码风格，比方说分号，空格
2. reducer中switch中可以拆分函数

#### 异步组件实现异步加载

React-loadable 实现详情页面进入之后再加载，减少单个文件的体积