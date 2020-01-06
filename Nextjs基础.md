## Nextjs基础

#### next作为koa中间件使用

```
# nextjs自身带有服务器的，但是只能处理ssr渲染，包括页面，路由渲染
# 缺陷：需要处理http请求，并根据请求数据返回响应的内容；根据域名之类的绑定服务器；数据接口；数据库连接；session状态，但是这些功能并不包含
# 解决方案：将next作为中间件，使用koa，express等扩展服务

# server.js
# 示例：
const Koa = require('koa')
const next = require('next')
// 模式
// 在没有设置的情况下 process.env.NODE_ENV 是 undefined，如果我们写成 === ‘development'，dev为false，next会认为是 NODE_ENV 是 production，运行会报错
const dev = process.env.NODE_ENV !== 'production'
const app = next({dev})
// 处理http请求响应
const handle = app.getRequestHandler()

// 等编译完成之后
app.prepare().then(()=>{
	const server = new Koa()
	server.use(async(ctx,next)=>{
		await handle(ctx.req,ctx.res)
		ctx.respond = false
	})
	
	server.listen(3000,()=>{
		console.log('koa server listening on port 3000')
	})
})
# koa respond 说明：为了绕过 Koa 的内置 response 处理，你可以显式设置 ctx.respond = false;。 如果您想要写入原始的 res 对象而不是让 Koa 处理你的 response，请使用此参数。
```

#### 项目目录

```
pages：里的每个js都是单独页面，路由也通过文件名和目录结构构成
lib：放置功能性代码
static：资源文件，图片，字体等等
components：组件
.next：自动生成的目录
next.config.js nextjs配置文件
```

#### 路由基础

```
# pages中的每个文件，都代表单独的页面

# nextjs提供Link组件提供前端路由跳转
# 示例：
import Link from 'next/link'
import PageTitle from './../components/PageTitle'

export default () => {
    return (
        <div>
            <PageTitle title={'Index'} />
            <Link href={'/test/pageB'}>
                <button>Page B</button>
            </Link>
            <Link href={'/pageA'}>
                <button>Page A</button>
            </Link>
        </div>
    )
}
# Link本身没有任何标签，所以Link内部必须接受一个渲染节点，children 并且是唯一节点，示例：Link内接收button作为操作节点
# href属性属性上的路径是区分大小写的，要和pages中定义的页面名称对应上，否则报404

# JS逻辑进行路由跳转，Router模块
import Router from 'next/router'

export default ()=>{
	const gotoB = ()=>{
		Router.push('/b')
	}
	return <Button onClick={gotoB}>跳转B</span>
}

```

#### 动态路由

```
# 由于路由结构是通过pages的目录结构映射出来的，所以只能使用query（？xxx）的方式传递参数
# 传递参数：
import Link from 'next/link'
import Router from 'next/router'
import PageTitle from './../components/PageTitle'

export default () => {

    const turnPageB = () => {
        Router.push({
            pathname: "/test/pageB",
            query: {
                name: 'haha',
                age: "25"
            }
        })
    }

    return (
        <div>
            <PageTitle title={'Index'} />
            <Link href={{
                pathname: '/pageA', query: {
                    name: 'zhuzhu',
                    age: '24'
                }
            }}>
                <button>Page A</button>
            </Link>
            <button onClick={turnPageB}>Page B</button>
        </div>
    )
}
# 获取参数
import PageTitle from './../components/PageTitle'
import { withRouter } from 'next/router'

const pageA = ({ router }) => {
    return (
        <>
            <PageTitle title={'Page A'} />
            <p>name:{router.query.name}</p>
            <p>age:{router.query.age}</p>
        </>
    )
}

export default withRouter(pageA)
```

#### 路由映射

```
# 解决 /article/:id nextjs 实现
# 显示/a/1 实际上是/a?id=1 并且可以通过 router.query.id 拿到对应的值
# 示例代码：
<Link href={{
	pathname: '/pageC', query: {
			id: 20,
			name: 'demo'
		}
	}} as={{
			pathname: `/pageC/${20}`, query: {
					name: 'demo'
			}
}}>
	<button>Page C</button>
</Link>
# 注：如果有参数需要传递，href和as属性中的query都需要定义，前者控制真正的参数传递，后者仅控制url显示，例如，如果as中没有定义query，那么url中不会显示query中的值，同理，如果href中没有定义query，及时as中定义了也没有作用，并不会传参过去，仅仅是看着是那么一回事
# OR
const turnPageD = () => {
        let id = 10
        Router.push({
            pathname: "/pageD",
            query: {
                id,
                name: 'lihai'
            }
        }, {
            pathname: `/pageD/${id}`,
            query: {
                name: 'lihai'
            }
        })
    }
# 存在的问题，不是真正的文件内容，pages中的文件结构不存在，直接通过路径访问会报404，上面的实现是因为前端路由跳转实现的，直接刷新后台服务是不知情的

# 解决方式：通过第三方服务（koa，express）设置对应的路由映射
const router = new Router()
		// 通过路径中包含正则的方式，匹配多个页面
    router.get('/(page[CD])/:id', async (ctx, next) => {
    		// 动态获取目标页面
        let pathname = ctx.url.match(/(^\/\w*)\/?/)[1]
        const id = ctx.params.id
        // 使用next中间件处理
        await handle(ctx.req, ctx.res, {
            pathname,
            query: { id, ...ctx.query }
        })
        ctx.respond = false
    })

    server.use(router.routes())

		// 加载next中间件
    server.use(async (ctx, next) => {
        await handle(ctx.req, ctx.res)
        ctx.respond = false
    })
# 注：装载next中间件放在koa-router路由匹配之后，放在最后
```

#### 路由变化钩子

```
# 观察钩子事件
const events = [
	'routeChangeStart',
	'routeChangeComplete',
	'routeChangeError',
	'beforeHistoryChange',
	'hashChangeStart',
	'hashChangeComplete'
]

const makeEvent =(type)=>{
	return (...args)=>{
		console.log(type,...args)
	}
}

event.forEach(event=>{
	Router.events.on(event,makeEvent(event))
})

```

#### nextjs数据获取方式

```
# 挂在组件上的静态方法，获取组件中的数据
# 1.在页面中获取数据	2.在App中获取数据
# 完成客户端和服务端数据的同步
# 只针对路由中对应的组件，也就是pages中的页面组件
# 示例：
const A =({name})=><span>{name}</span>

// 同步数据
// getinitialProps函数的返回值会作为页面的props
A.getInitialProps =()=>{
	// 如果是前端跳转，那么这个值由前端渲染
	// 如果直接刷新页面，那么这个值由后台渲染好之后传给前台，前台不会再次进行渲染
	console.log('---------------------------')
	return {
		name:'zhuzhu'
	}
}

// 异步数据
A.getInitialProps = async () =>{
	let promise = new Promise((resolve,reject)=>{
		setTimeout(()=>{
			resolve({name:'zhuzhu'})
		},2000)
	})
	return await promise
}

# 说明：异步数据的执行情况：会先等待2s然后才会路由改变跳转到指定的页面，这种渲染方式更像传统网页的渲染方式，直到后台组装完数据返回了才会渲染页面，nextjs结合和spa的优势和传统页面的优势

```

#### 集成Antd

```
# nextjs默认是不支持css文件import的
# nextjs提供了插件，默认有一个cssinjs方案的
# 安装@zeit/next-css
# 添加next.config.js
const withCSS = require('@zeit/next-css')

if (typeof require !== 'undefined') {
    require.extensions['.css'] = file => { }
}

module.exports = withCSS({
	// cssModules:true  // 如果添加上去之后，控制它会报错
})
# withCSS返回config对象，包含对css的配置，如果只有有其他的修改，通过层层包裹即可，例如：withLess(withCss({}))

# 继承antd design
# 通过babel-plugin-import 实现按需加载
{
		// 必须的babel配置，next默认的babel配置
    "presets": ["next/babel"],
    "plugins": [
        [
            "import",
            {
                "libraryName": "antd",
                "style":"css",
                "style":true // 用true会报错，import js and css modularly (LESS/Sass source files)，我们还没有多less/sass作任何支持
            }
        ]
    ]
}
# 创建_app.js覆盖app组件，引入antd css样式，或者.babelrc文件中配置style：‘css'，但是这样可能会和min-css-extract-plugin 报冲突
# _app.js文件如下：
// 导入nextjs默认App组件
import App from 'next/app'
// 添加css样式
import 'antd/dist/antd.css';

export default App

```

#### nextjs自定义App

```
# 通过在pages页面中写自定义的_app.js，实现覆盖nextjs默认的App组件
# 作用：
1.固定Layout，整体的页面布局框架
2.保持一些公用的状态，比如redux使用
3.给页面传入一些自定义数据
4.自定义错误处理

# 示例：
# 创建
_app.js
import App,{Container} from 'next/app'
import 'antd/dist/antd.css'

class MyApp extends App{
	render(){
		// 页面组件
		const {Component,pageProps} = this.props
		return (
			<Container>
				<Component {...pageProps} />
			</Container>
		)
	}
}
export default MyApp

# 以上代码是最基础的，仅仅是继承了nextjs中定义的App，并没有添加自定义的功能

# 自定义的MyApp必须实现getInitialProps方法，这样子组件中用getInitialProps才能有效，这个方法默认的App也是实现了的，所以子组件的getInitialProps静态方法才能有效
# 实现代码如下：

import App, { Container } from 'next/app'
import 'antd/dist/antd.css'

class MyApp extends App {

    // 必须实现该方法，通过App将每个页面组件的props加载到组件上
    static async getInitialProps({ Component, ctx }) {
        let pageProps
        if (Component.getInitialProps) {
            pageProps = await Component.getInitialProps(ctx)
        }
        return {
            pageProps
        }
    }

    render() {
        // 页面组件
        const { Component, pageProps } = this.props
        return (
            <Container>
                <Component {...pageProps} />
            </Container>
        )
    }
}

export default MyApp

# MyApp可用于编写整体布局，用来包裹Component组件，这样只需要显示子页面的内容即可
```

#### 自定义document

```
# 只有在服务端渲染的时候才会调用的组件
# 用来修改服务端渲染的文档内容
# 一般配合第三方css-in-js来使用

# 同app一样需要复写document的组件
# 示例：
# _document.js
import Document,{Html,Head,Main,NextScript} from 'next/document'

class Mydocument extends Document{
	render(){
		return (
			<Html>
				<Head>
				
				</Head>
				<body>
					<Main />
					<NextScript />
				</body>
			</Html>
		)
	}
}
# 以上代码是最基础的实现，自定的document组件也同样是这样定义的
# 作用：定义document，但是仅仅在服务端渲染的时候起作用，如果是前端渲染，不会有用
# 部分覆盖
import Document, { Html, Head, Main, NextScript } from 'next/document'

class MyDocument extends Document {

    static async getInitialProps(ctx) {
        const props = await Document.getInitialProps(ctx)
        return {
            ...props
        }
    }

    render() {
        return (
            <Html>
                <Head>
                    {/* 刷新页面可以看出，只有服务端渲染才会有效 */}
                    <title>My Next Demo</title>
                    <style>{`.text{color:red}`}</style>
                </Head>
                <body>
                    <Main />
                    <NextScript />
                </body>
            </Html>
        )
    }
}

export default MyDocument
# getInitialProps 和 render 方法都是Document组件原本就实现的，如果自定义要扩展内容，要把最起码的基础代码实现了，以上代码就是最基础的实现，要么不覆盖，要么就要把基础的实现好

# 不推荐在_document.js中定义页面标签，应该在每个page页面内，引入 import Head from '@next/head' 然后在head内定义title
```

#### 定义样式

```
# css-in-js方案
# nextjs内默认style-jsx方式
# 示例：
const A = (props) => (
	<>
		....
		<style jsx>
		{`
			...直接写样式就可以，也可以是类等css选择器
		`}
		</style>
		<style jsx global>
		{`
			...直接写样式，全局样式
		`}
		</style>
	</>
)
# 说明：组件和组件之间是隔离的，文件作用域内有作用
# 会根据文件生成最高层的class，通过class实现样式隔离
# 全局样式限制：全局样式存在期间：对应样式的组件渲染之后，全局样式会起作用，如果组件卸载之后，全局样式也会被移除
# 样式的展示和不展示，通过组件是否渲染来控制
# 样式在字符串模版内，可以使用外部变量
# 优势：包含到服务端渲染内，体验很好，不会因为前端js加载dom的问题，造成样式显示延迟
```

#### 使用styled-component

```
# 集成第三方css-in-js方案
# 通过_document.js增强
# 示例：
import Document,{Html,Head,Main,NextScript} from 'next/document'

class Mydocument extends Document{

	static getInitialProps(ctx){
	
		// 增强渲染方法
		const originalRenderPage = ctx.renderPage
		// 再次包装
		ctx.renderPage = () => originalRenderPage({
			// 返回对App增强的App，如果定义了自定义的App，App就是自定义的，否则，App默认next中的App，
			enhanceApp:App => App,
			// 返回对当前路由组件的增强；Component对应的是当前路由的page中的页面组件
			enhanceComponent:Component => Component
		})
	
		const props = await Document.getInitialProps(ctx)
		return {
			...props
		}
	}
	
	render(){
		return (
			<Html>
				<Head>
					<style>{`.text{color:red}`}</style>
				</Head>
				<body>
					<Main />
					<NextScript />
				</body>
			</Html>
		)
	}
}
# nextjs中采用了很多high out component的开发模式

# 示例：hoc
import Document, { Html, Head, Main, NextScript } from 'next/document'

function withLog(Comp) {
    return (props) => {
        console.log(props)
        return <Comp {...props} />
    }
}

class MyDocument extends Document {

    static async getInitialProps(ctx) {

        const originalRenderPage = ctx.renderPage
        ctx.renderPage = () => originalRenderPage({
            enhanceApp: App => App,
            enhanceComponent: Component => withLog(Component)
        })

        const props = await Document.getInitialProps(ctx)
        return {
            ...props
        }
    }

    render() {
        return (
            <Html>
                <Head>
                    <style>{`.text{color:red}`}</style>
                </Head>
                <body>
                    <Main />
                    <NextScript />
                </body>
            </Html>
        )
    }
}
# 用来增强包装的Component，返回一个函数组件，强化之后，渲染对应的组件的时候调用
# 思路：css-in-js 服务端渲染，对对应的组件进行增强，之后再具体进行渲染操作

# 集成styled-components
# 安装styled-components 和 对应的babel组件 babel-plugin-styled-components
yarn add styled-components -S
yarn add babel-plugin-styled-components -D
# 代码集成
# .babelrc
{
	"plugins":[
		["styled-components",{"ssr":true}]
	]
}

# _document.js
import Document,{Html,Head,Main,NextScript} from 'next/document'
import {ServerStyleSheet} from "styled-components"

class Mydocument extends Document{

	static getInitialProps(ctx){
	
		const sheet = new ServerStyleSheet()
	
		// 增强渲染方法
		const originalRenderPage = ctx.renderPage
		
		try{
      // 再次包装
      ctx.renderPage = () => originalRenderPage({
        // 返回对App增强的App，如果定义了自定义的App，App就是自定义的，否则，App默认next中的App，
        // sheet上挂载app中有用到的styled-components代码
        enhanceApp:App => (props)=> sheet.collectStyles(<App {...props} />)
      })
      
      const props = await Document.getInitialProps(ctx)
      return {
        ...props,
        // 第一部分：nextjs 内置的 styledjsx 生成样式
        // 第二部分：styled-components 产生的样式
        styles:<>{props.styles}{sheet.getStyleElement()}</>
      }
		}finally{
			sheet.seal()
		}
	}
	
	render(){
		return (
			<Html>
				<Head>
					<style>{`.text{color:red}`}</style>
				</Head>
				<body>
					<Main />
					<NextScript />
				</body>
			</Html>
		)
	}
}

# a.js  styled-components 使用方式
import { withRouter } from 'next/router'
import styled from 'styled-components'
import PageTitle from '../components/PageTitle'

const Title = styled.h1`
    color:yellow;
    font-size:40px;
`

const PageC = ({ router }) => {
    return (
        <>
            <PageTitle title={'Page C'} />
            <Title>this is a title</Title>
            <p>ID:{router.query.id}</p>
            <p>name:{router.query.name}</p>
        </>
    )
}

export default withRouter(PageC)

```

#### LazyLoading

```
# nextjs默认已经把所有的页面切割成不同的js模块，直到路由加载页面，才会加载模块加载

# 异步加载模块
# 场景：现在需要引入moment模块，如果很多的模块都有使用moment模块，那么在打包的时候，会将第三方的模块打包到公有模块中，如果现在用户访问的模块很多都用不到moment，我们希望在用到的时候，再将moment打包进去
# 示例：
PageD.getInitialProps = async () => {
    const moment = await import('moment')
    return {
        initTime: moment.default(Date.now() - 60 * 1000).fromNow()
    }
}

# 异步加载组件
import { withRouter } from 'next/router'
import dynamic from 'next/dynamic'
import PageTitle from '../components/PageTitle'

const LazyComp = dynamic(import('../components/LazyComp.jsx'))

const PageD = ({ router, initTime }) => {
    return (
        <>
            <PageTitle title={'Page D'} />
            <LazyComp />
            <p>ID:{router.query.id}</p>
            <p>name:{router.query.name}</p>
            <p>{initTime}</p>
        </>
    )
}
# 这样直到render函数执行到渲染组件的时候才会加载组件
```

#### next.config.js

```
# 配置如下
const withCss = require('@zeit/next-css')

const config = {
	// 编译文件的输出目录
	distDir: 'dest',
	// 是否给每个路由生成Etag（用来缓存验证，如果请求了一个页面，下次再次请求一个页面，对比两个etag，如果相同，那么服务端就不再返回内容，让浏览器使用缓存）；默认开启etag，如果我们配置有nginx的etag配置，可以关闭
	generateEtage:true,
	// 页面内容缓存设置（只是针对开发时环境）
	onDemanEntries:{
		// 内容在内存中缓存的时长（ms）
		maxInactiveAge:25*1000,
		// 同时缓存多少个页面
		pagesBufferLength:2
	},
	// 在pages目录下那种后缀的文件会被认为是页面
	pageExtensions:['jsx','js'],
	// 配置buildId，创建build的时候的ID，使用场景，对同一个任务部署在不同节点的时候会用到
	generateBuildId:async()=>{
		if(process.env.YOUR_BUILD_ID){
			return process.env.YOUR_BUILD_ID
		}
		// 返回null使用默认的unique id
		return null
	},
	// 手动修改webpack config
	webpack(config,options){
		return config
	},
	// 修改webpackDevMiddleware配置
	webpackDevMiddleware:config=>{
		return config
	},
	// 可以在页面上通过 process.env.customKey 获取 value
	env:{
		customKey:'value'
	},
	// 下面两个要通过‘next/config’来读取
	// 只有在服务端渲染时才会获取的配置
	serverRuntimeConfig:{
		mySecret:'scret',
		secondSecret:process.env.SECOND_SECRET
	},
	// 在服务端渲染和客户端渲染都可获得的配置
	publicRuntimeConfig:{
		staticFolder:'/static'
	},
}

if(typeof require !== 'undefined'){
	require.extensions['.css'] = file =>{}
}

module.exports = withCss(config)

# serverRuntimeConfig,publicRuntimeConfig 示例：
# 具体页面
import getConfig from 'next/config'
const {serverRuntimeConfig,publicRuntimeConfig} = getConfig()
# 可打印查看这两个对象，serverRuntimeConfig和pubicRuntimeConfig都可以在后台控制台打印，
# 浏览器端只能打印publicRuntimeConfig
# 以上两个配置项，要根据具体的使用情况来使用，有些配置项只是对服务端可用，前端并不需要知道

```

#### SSR流程

```
# 流程：
# 服务端渲染
1.浏览器发起page请求
2.koa接收请求，并调用nextjs
3.nextjs开始渲染
4.调用（app,document）的getInitialProps
5.调用页面的getInitialProps
6.渲染出最终的html
7.结束

# 浏览器渲染
1.点击链接按钮
2.异步加载页面的组件js
3.调用页面的getInitialProps
4.数据返回，路由变化
5.渲染页面
6.结束

# 规范：无论是服务端渲染还是客户端渲染，都要将数据的获取放到getInitialProps中处理

```

