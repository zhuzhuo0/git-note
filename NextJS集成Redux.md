## NextJS集成Redux

#### Nextjs中的HOC模式

```
# react社区积累的开发模式
# 接收组件作为参数，并返回新的组件 例如：react-redux中的connect函数
# 示例代码：
# /lib/test-hoc.js
export default (Comp) => {
    // 包裹MyApp组件，MyApp组件中的props包含Component，pageProps和其他的组件
    // 这些都需要传递给包含的组件
    const TestHocComp = ({ Component, pageProps, ...rest }) => {
    		// 或者通过(props)=>{
    		//	<Comp {...props} />
    		//}
    		// 这种方式，把所有的需要传入的props都传入组件中
        console.log(Component, pageProps)
        // 不是每个页面都存在pageProps
        if (pageProps) {
            pageProps.test = '123'
        }
        return < Comp Component={Component} pageProps={pageProps} {...rest} />
    }
    // 包含的组件中的getInitailProps方法需要添加，否则nextjs无法执行getInitialProps方法
    TestHocComp.getInitialProps = Comp.getInitialProps
    
    return TestHocComp
}
# 注意：nextjs中对App这个组件使用HOC时，需要注意App上的getInitialProps静态方法，HOC必须复制App中的getInitialProps方法，这样我们返回的组件中是没有getInitialProps方法的；nextjs执行getInitialProps方法的时候，在我们的TestHOcComp上找不到，就会出现问题

# 通过HOC的方式集成Redux，将不同的逻辑和功能拆分到不同的HOC中，一层一层的包裹组件，将不同的逻辑隔离开

```

#### NextJS集成redux

```
# 思考问题：
1.服务端如何写入数据到store
2.如何同步服务端的数据到客户端
# 思路：
在启动服务端服务的时候，服务端会创建store对象，同时由于服务端是一直运行的，所以store对象会一直存在，并且每次浏览器对服务端请求页面的时候，公用的就会是同一个store，这样就会造成数据问题，比如：我们启动服务器，然后请求页面，控制台不会报warning，因为这时候store也是刚刚创建，会有从初始值到最终值的过程，但是当我们再次请求的时候，此时的值已经是最终值了，但是浏览器端（客户端）还会是初始值，有这么一个变化的过程，这就造成了客户端和服务端数据不同步的问题
我们可以通过每次服务端渲染的时候就重新创建store，这样我们只需控制这个store中的数据，就能保持数据的同步

# 通过HOC的方式集成redux，这样分离出redux功能，不会和其他的HOC功能混淆

# /store/store.js
import { createStore, combineReducers, applyMiddleware } from 'redux'
import ReduxThunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension';

const initialState = {
    count: 0
}

const userInitialState = {
    name: 'zhuzhu',
    age: 24,
}

const ADD = 'ADD'

//action creater
export const add = (num) => ({
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

const allReducer = combineReducers({
    counter: counterReducer,
    user: userReducer
})

export default (state) => {
    const store = createStore(
        allReducer,
        Object.assign({}, {
            counter: initialState,
            user: userInitialState
        }, state),
        composeWithDevTools(applyMiddleware(ReduxThunk)))
    return store
}

# /lib/with-redux.js
import React from 'react'
import createStore from '../store/store'

const isServer = typeof window === 'undefined'
const __NEXT_REDUX_STORE__ = '__NEXT_REDUX_STORE__'

const getOrCreateStore = (initialState) => {
    if (isServer) {
        return createStore(initialState)
    }
    if (!window[__NEXT_REDUX_STORE__]) {
        window[__NEXT_REDUX_STORE__] = createStore(initialState)
    }
    return window[__NEXT_REDUX_STORE__]
}

export default (Comp) => {

    class WithRedux extends React.Component {
        constructor(props) {
            super(props)
            // 在创建WithRedux组件的时候创建store
            this.reduxStore = getOrCreateStore(props.initialReduxState)
        }

        render() {
            const { Component, pageProps, ...rest } = this.props
            return <Comp Component={Component} pageProps={pageProps} {...rest} reduxStore={this.reduxStore} />
        }
    }

    // 服务端渲染还是客户端渲染都会执行的方法，所以用它来创建store非常合适
    // 服务端渲染时，执行getInitialProps返回的对象最终会序列化为字符串，包含在返回的html中
    // 客户端读取到返回的对象序列，转换成对象，在用数据生成store
    // 为什么不直接返回store对象，而是返回store中的state？
    // 因为store是一个对象，里面包含很多方法，这些方法在序列化的过程中会出错，不容易转换正确
    // 客户端页面跳转也会执行这个方法
    WithRedux.getInitialProps = async (ctx) => {
        // 服务端渲染时每次都是创建新的store
        // 客户端渲染时只在没有的情况下创建新的store
        // *这个位置可以添加默认的数据
        const reduxStore = getOrCreateStore()

        ctx.reduxStore = reduxStore;

        let appProps = {}
        if (typeof Comp.getInitialProps === 'function') {
            appProps = await Comp.getInitialProps(ctx)
        }

        return {
            ...appProps,
            initialReduxState: reduxStore.getState()
        }
    }
    return WithRedux
}

# /pages/_app.js
import App, { Container } from 'next/app'
import 'antd/dist/antd.css';
import Layout from './../components/Layout'
import { Provider } from 'react-redux'
import WithRedux from '../libs/with-redux'

class MyApp extends App {

    static async getInitialProps(ctx) {
        const { Component } = ctx
        let pageProps = {}
        if (Component.getInitialProps) {
            pageProps = await Component.getInitialProps(ctx)
        }
        return {
            pageProps
        }
    }

    render() {
        const { Component, pageProps, reduxStore } = this.props
        return (
            <Container>
                <Layout>
                    <Provider store={reduxStore}>
                        <Component {...pageProps} />
                    </Provider>
                </Layout>
            </Container>
        )
    }
}

export default WithRedux(MyApp) 

# /pages/redux.js
import { connect } from 'react-redux'
import { add } from '../store/store'

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

// 作初始化服务端渲染
ReduxComp.getInitialProps = async ({ reduxStore }) => {
    reduxStore.dispatch(add(8))
    return {}
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

#### 获取用户信息，并存储在session中

```
const axios = require('axios')
const config = require('../config')

const { client_id, client_secret, request_token_url } = config.github

module.exports = (server) => {
    server.use(async (ctx, next) => {
        if (ctx.path === '/auth') {
            const code = ctx.query.code
            if (!code) {
                ctx.body = 'code not exist'
                return
            }
            const result = await axios({
                method: "POST",
                url: request_token_url,
                data: {
                    client_id,
                    client_secret,
                    code
                },
                headers: {
                    Accept: 'application/json'
                }
            })
            // console.log(result.status, result.data, result.data.error)
            if (result.status === 200 && (result.data && !result.data.error)) {
                ctx.session.githubAuth = result.data
                const { access_token, token_type } = result.data
                const userInfoResp = await axios({
                    method: "GET",
                    url: 'https://api.github.com/user',
                    headers: {
                        "Authorization": `${token_type} ${access_token}`
                    }
                })
                ctx.session.userInfo = userInfoResp.data;
                ctx.redirect('/')
            } else {
                const errorMsg = result.data && result.data.error
                ctx.body = `request token failed ${errorMsg}`
            }
        } else {
            await next()
        }
    })
}

# 说明：
axios作为http库使用，将access_token存在session中，session存储与redis中，在每次服务端和github发送消息的时候带上token，并将数据存储与session中

```

