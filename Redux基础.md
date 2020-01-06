## Redux基础

#### Redux基础

```
# redux本身是独立于UI框架的，是一个单向数据流框架
# 可以独立使用
# 示例代码：
import { 
	createStore, 
	// 合并reducer
	combineReducers, 
	// redux中间件
	applyMiddleware 
} from 'redux'
// 扩展dispatch方法，使其接收一个方法作为参数，这样可以执行异步代码
import ReduxThunk from 'redux-thunk'

// 分模块的初始值
const initialState = {
    count: 0
}

const userInitialState = {
    name: 'zhuzhu',
    age: 24,
}

// action Type
const ADD = 'ADD'

// action creater （一般方式，通过函数返回对象，对象中一般包含type和参数）
const add = (num) => ({
    type: ADD,
    num
})

// 判断返回的是否是一个方法，然后将store的dispatch和getState方法传入到函数中
// 然后执行函数，在异步回调中调用dispatch方法
const addAsync = (num) => {
    return (dispatch, getState) => {
        setTimeout(() => {
            dispatch(add(num))
        }, 5000)
    }
}

const counterReducer = (state = initialState, action) => {
    console.log(action)
    switch (action.type) {
        case ADD:
            return { count: state.count + (action.num || 1) }
        default:
            return state
    }
}

const UPDATE_NAME = 'UPDATE_NAME'

const userReducer = (state = userInitialState, action) => {
    switch (action.type) {
        case UPDATE_NAME:
            return {
                ...state,
                name: action.name
            }
        default:
            return state;
    }
}

// 合并reducer，并通过key划分不同的模块，使state分离
const allReducer = combineReducers({
    counter: counterReducer,
    user: userReducer
})

const store = createStore(allReducer, {
    counter: initialState,
    user: userInitialState
}, applyMiddleware(ReduxThunk))

// reducer
// 1.纯函数，只依赖传入的参数
// 2.返回新的对象，内存地址不同
// 3.通过combineReducer来合并reducer

// 订阅之前的dispatch操作会被忽略
store.dispatch({ type: ADD })

// 订阅改变
store.subscribe(() => {
    console.log('change', store.getState())
})

store.dispatch(add(5))

store.dispatch(addAsync(10))

store.dispatch({ type: UPDATE_NAME, name: 'haha' })

export default store
```

#### React-redux

```
# react-redux 主要是封装了subscribe方法，提供更新store的机制
# 全局定义store
# 代码：
# /pages/_app.js
import App, { Container } from 'next/app'
import 'antd/dist/antd.css';
import Layout from './../components/Layout'
import { Provider } from 'react-redux'
import store from '../store/store'

class MyApp extends App {

    static async getInitialProps(ctx) {
        const { Component } = ctx
        console.log('app init')
        let pageProps
        if (Component.getInitialProps) {
            pageProps = await Component.getInitialProps(ctx)
        }
        return {
            pageProps
        }
    }

    render() {
        const { Component, pageProps } = this.props
        return (
            <Container>
                <Layout>
                    <Provider store={store}>
                        <Component {...pageProps} />
                    </Provider>
                </Layout>
            </Container>
        )
    }
}

export default MyApp

# 需要store映射的页面
# /pages/redux.js
# 示例代码：
import { connect } from 'react-redux'

const ReduxComp = (props) => {

    let { count, name, add, rename } = props

    return (
        <>
            <p>{count}</p>
            <input onChange={(e) => {
                rename(e.target.value)
            }} value={name} />
            <button onClick={add}>Add Btn</button>
        </>
    )
}

// connect方法返回的是一个包裹后的组件
// high order component
// 一个函数，以其他组件为参数，返回新的组件，称之为HOC
export default connect(
    // 映射store中的state
    (state) => {
        return {
            count: state.counter.count,
            name: state.user.name
        }
    },
    // 映射对应的reducer
    (dispatch) => {
        return {
            add: () => { dispatch({ type: 'ADD' }) },
            rename: (name) => { dispatch({ type: 'UPDATE_NAME', name }) }
        }
    }
)(ReduxComp)

```

#### Redux-dev-tool

```
# chrome插件
# 监控：store变化过程，以及具体action，数据前后变化
# 同时项目中需要安装redux-devtools-extension
# 使用：包裹store中的applyMiddleware函数
# 示例代码：
# /store/store.js
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(allReducer, {
    counter: initialState,
    user: userInitialState
}, composeWithDevTools(applyMiddleware(ReduxThunk)))

```

