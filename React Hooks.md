## React Hooks

#### 基本介绍

```
# 介绍：让函数组件具有类组件的能力
# 场景：计数器，每秒加1
# 原来的写法：
import React,{Component} from 'react'

export default class Counter extends Component{
	state = {
		count:0
	}
	
	componentDidMount(){
		this.timer = setInterval(()=>{
			this.setState({count:count+1})
		},1000)
	}
	
	componentWillUnmount(){
		clearInterval(this.timer)
	}
	
	render(){
		return <span>{this.state.count}</span>
	}
}

# 现在的写法：
# 注：hooks功能是16.8之后提供的
import React, {useState,useEffect} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	
	useEffect(()=>{
		const timer = setInterval(()=>{
			setCount(c=>c+1)
		},1000)
		return ()=> clearInterval(timer)
	},[])
	
	return <span>{count}</span>
}

```

#### State Hooks

```
# useState
# 示例：
import React, { useState } from "react";

const CounterHock = () => {
  const [count, setCount] = useState(0);

  const add = () => {
    // 形式1
    setCount(count + 1);
  };

  const reduce = () => {
    // 形式2
    setCount(c => c - 1);
  };

  return (
    <div>
      <p>{count}</p>
      <button onClick={add}>+</button>
      <button onClick={reduce}>-</button>
    </div>
  );
};
# 关键代码：
const [count,setCount] = useState(0) // useState返回数组形式，初始化count和setCount
setCount(1) // 直接设置新的值
setCount(c=>c+1) // 在原有数据上做处理

# useReducer
# 关键代码：
const countReducer = (state,action) => {
	switch(action.type){
		case 'add':
			return state + 1
		case 'minus':
			return state - 1
		default:
			return state
	}
}
const [count,dispatchCount] = useReducer(countReducer,0)
dispatchCount({type:'minus'})

# 基本参数示例代码：
import React, { useReducer } from "react";

const initialState = { count: 0 };

const reducer = (state, action) => {
  switch (action.type) {
    case "reduce":
      return { count: state.count - 1 };
    case "add":
      return { count: state.count + 1 };
    default:
      return state;
  }
};

const CounterReducer = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const add = () => {
    dispatch({ type: "add" });
  };

  const reduce = () => {
    dispatch({ type: "reduce" });
  };

  return (
    <div>
      <p>{state.count}</p>
      <button onClick={add}>+</button>
      <button onClick={reduce}>-</button>
    </div>
  );
};

export default CounterReducer;

# 完整参数示例代码：
import React, { useReducer } from "react";

const init = initialCount => {
  return { count: initialCount };
};

const reducer = (state, action) => {
  switch (action.type) {
    case "reduce":
      return { count: state.count - 1 };
    case "add":
      return { count: state.count + 1 };
    case "reset":
      return init(action.payload);
    default:
      return state;
  }
};

const CounterReducer = ({ initialCount }) => {
  const [state, dispatch] = useReducer(reducer, initialCount, init);

  const add = () => {
    dispatch({ type: "add" });
  };

  const reduce = () => {
    dispatch({ type: "reduce" });
  };

  const reset = () => {
    dispatch({ type: "reset", payload: initialCount });
  };

  return (
    <div>
      <p>{state.count}</p>
      <button onClick={add}>+</button>
      <button onClick={reduce}>-</button>
      <button onClick={reset}>reset</button>
    </div>
  );
};

export default CounterReducer;

# 说明：
# 简单的数据通过useState可以很方便的处理，只要注意下闭包问题就行，对于复杂的数据，比方说state为对象的形式，通过useReducer更好处理

```

#### Effect Hooks

```
# effect Hocks类似componentDidMount和componentDidUpdate，注册的函数将会在绘制完毕之后执行，这里通常可以做些订阅事件，计时器之类的事情，当函数做的是订阅或者计时器之类的事情时，可以通过返回一个函数的方式，在返回的函数内执行取消订阅或者销毁计时器的操作；同时，为了减少不必要的操作，通过第二个参数，数组的形式，说明只有在哪些状态发生改变的时候才执行注册的函数

# 示例：
useEffect(()=>{
	const timer = setInterval(()=>{
		dispatchCount({type:'minus'})
	},1000)
	return ()=>clearInterval(timer)
},[])

# 场景01:
import React, {useState,useEffect} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	const [name,setName] = useState('zhuzhu')
	
	useEffect(()=>{
		console.log('effect invoked')
		return ()=> console.log('effect deteched')
	})
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<button onClick={e=>setCount(c=>c+1)}>{count}</button>
		</div>
	)
}
# 执行结果：点击按钮或者修改input中的内容，重新渲染Counter组件，useEffect也会被调用，卸载组件的时候也会执行‘effect deteched’，当然也会对响应的事件卸载等等操作

# 场景02:
import React, {useState,useEffect} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	const [name,setName] = useState('zhuzhu')
	
	useEffect(()=>{
		console.log('effect invoked')
		return ()=> console.log('effect deteched')
	},[])
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<button onClick={e=>setCount(c=>c+1)}>{count}</button>
		</div>
	)
}
# 执行结果：Counter函数只有在最开始的时候执行一次，打印‘effect invoked’，和卸载组件的时候执行打印’effect deteched‘ 无论我们怎样操作，都不会让组件重新渲染，类似用class的方式；
# 解释下闭包陷阱，之前的递增函数，setInterval中的函数，由于useEffect函数只会执行一次，内部定义的循环函数始终处于闭包内，这样其中的count会一直是初始值，形成闭包，保存了当时的外部环境变量

# 场景03:
import React, {useState,useEffect} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	const [name,setName] = useState('zhuzhu')
	
	useEffect(()=>{
		console.log('effect invoked')
		return ()=> console.log('effect deteched')
	},[name])
	
	// useEffect(()=>{
	// 	 console.log('effect invoked')
	// 	 return ()=> console.log('effect deteched')
	// },[count])
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<button onClick={e=>setCount(c=>c+1)}>{count}</button>
		</div>
	)
}
# 执行结果：useEffect第二个参数内包含的变量，如果值改变了，就会使组件函数重新渲染（销毁 + 挂载），React推荐对改变中涉及的变量都放在这个参数内

# useLayoutEffect
# 示例：
import React, {useState,useEffect,useLayoutEffect} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	const [name,setName] = useState('zhuzhu')
	
	useEffect(()=>{
		console.log('effect invoked')
		return ()=> console.log('effect deteched')
	},[name,count])
	
	useLayoutEffect(()=>{
	 	console.log('layout effect invoked')
	 	return ()=> console.log('layout effect deteched')
	},[name,count])
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<button onClick={e=>setCount(c=>c+1)}>{count}</button>
		</div>
	)
}
# 执行说明：在useEffect执行之前会执行useLayoutEffect，结果明天执行以下
# 结果：
LayoutEffect.jsx:14 layout effect invoked
LayoutEffect.jsx:9 effect invoked
// 操作
LayoutEffect.jsx:15 layout effect deteched
LayoutEffect.jsx:14 layout effect invoked
LayoutEffect.jsx:10 effect deteched
LayoutEffect.jsx:9 effect invoked
// 操作
LayoutEffect.jsx:15 layout effect deteched
LayoutEffect.jsx:14 layout effect invoked
LayoutEffect.jsx:10 effect deteched
LayoutEffect.jsx:9 effect invoked
# 说明：useLayoutEffect执行的时机，是在任何属性更新之后，计算新的节点树，在没有正式更新dom之前执行
# 很少用，如果useLayoutEffect中做了很多计算，节点渲染必须在useLayoutEffect执行之后，这样就会造成卡顿；除非确认必须在渲染前执行，否则不要使用

```

#### Context Hooks

```
# 要求使用最新的api
# my-context.js
import React from 'react'
export default React.createContext('')
# 外层组件
import MyContext from '../lib/my-context'
...
<MyContext.Provider value="test">
	...内层组件
</MyContext>
# 内层组件使用
import React, {useContext} from 'react'
import MyContext from '../lib/my-context'

const context = useContext(MyContext)
# 获取的就是context值

```

#### Ref Hooks

```
# 即将被废除的ref string方式
# dom上
<span ref={'haha'} >demo</span>
# 获取dom
this.refs.haha

# 常规使用方式
# 定义：
constructor(props){
	super(props)
	this.ref = React.createRef()
}

componentDidMount(){
	this.ref.current // 这样就能取到对应的dom
}

render(){
	return <span ref={this.ref}>demo</span>
}

# function Component
import React, {useState,useEffect,useRef} from 'react'

const Counter = () => {
	// 形式：count 对应 setCount，名字可以不这样，效果时，后一个是前一个的操作函数；解构赋值对应起来
	const [count,setCount] = useState(0); 
	const [name,setName] = useState('zhuzhu')
	const inputRef = useRef()
	
	useEffect(()=>{
		console.log('effect invoked')
		console.log(inputRef)
		return ()=> console.log('effect deteched')
	},[name,count])
	
	useLayoutEffect(()=>{
	 	console.log('layout effect invoked')
	 	return ()=> console.log('layout effect deteched')
	},[name,count])
	
	return (
		<div>
			<input ref={inputRef} value={name} onChange={e=>setName(e.target.value)} />
			<button onClick={e=>setCount(c=>c+1)}>{count}</button>
		</div>
	)
}

```

#### 优化Hooks

```
# 原代码
import React,{useReducer,useState} from 'react'

const countReducer = (state,action) => {
	switch(action.type){
		case 'add':
			return state + 1
		case 'minus':
			return state - 1
		default:
			return state
	}
}

const Counter = () =>{
	const [count,dispatchCount] = useReducer(countRender,0)
	const [name,setName] = useState('zhuzhu')
	
	const config = {
		text:`count is ${count}`,
		color:count > 3 ? 'red':'blue'
	}
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<Child config={config} onButtonClick={()=>dispatchCount({type:'add'})} />
		</div>
	)
}

const Child = ({onButtonClick,config}) =>{
	console.log('child render')
	return (
		<button onClick={onButtonClick} style={{color:config.color}} />
			{config.text}
		</button>
	)
}

# 优化要求：现在每次点击button或者input输入都会造成Child组件重新渲染，button点击重新渲染是没有问题的，但是input输入实际上是不需要重新渲染Child组件，这样就会造成性能上的浪费，现在要实现类似shouldComponentUpdate的作用，input输入的时候不用重新渲染Child

# 优化后的代码
import React,{useReducer,useState,meno,useMeno,useCallback} from 'react'

const countReducer = (state,action) => {
	switch(action.type){
		case 'add':
			return state + 1
		case 'minus':
			return state - 1
		default:
			return state
	}
}

const Counter = () =>{
	const [count,dispatchCount] = useReducer(countRender,0)
	const [name,setName] = useState('zhuzhu')
	
	const config = useMemo(()=>({
		text:`count is ${count}`,
		color:count > 3 ? 'red':'blue'
	}),[count])
	
	// useCallback 就是 useMemo 的简化使用
	const handleButtonClick = useCallback(()=>dispatchCount({type:'add'}),[])
	const handleButtonClick = useMeno(()=>()=>dispatchCount({type:'add'}),[])
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<Child config={config} onButtonClick={handleButtonClick} />
		</div>
	)
}

const Child = meno(({onButtonClick,config}) =>{
	console.log('child render')
	return (
		<button onClick={onButtonClick} style={{color:config.color}} />
			{config.text}
		</button>
	)
})

# 优化说明：
1.分析下造成Child改变的原因：首先，每次改变重新渲染Counter函数组件是正确的，同时由于每次Counter函数组件的重新执行，都会重新定义config对象，所以每次Counter的重新渲染，传入到Child中的config都是新的对象，因此造成Child重新渲染，onButtonClick亦是如此
2.meno包裹Child组件;用useMeno使config对象不再新建，用法同useEffect存在第二个参数，内容是config改变时的依赖，当前指的是count变量
3.onButtonClick也同样需要处理，用useCallback，同样有第二个参数表示依赖，但是这个事件没有需要任何依赖，所以不需要第二个组件，始终是同一个对象

```

#### 闭包陷阱

```
# 示例：
import React,{useReducer,useState,meno,useMeno,useCallback} from 'react'

const countReducer = (state,action) => {
	switch(action.type){
		case 'add':
			return state + 1
		case 'minus':
			return state - 1
		default:
			return state
	}
}

const Counter = () =>{
	const [count,dispatchCount] = useReducer(countRender,0)
	const [name,setName] = useState('zhuzhu')
	
	const config = useMemo(()=>({
		text:`count is ${count}`,
		color:count > 3 ? 'red':'blue'
	}),[count])
	
	// useCallback 就是 useMemo 的简化使用
	const handleButtonClick = useCallback(()=>dispatchCount({type:'add'}),[])
	const handleButtonClick = useMeno(()=>()=>dispatchCount({type:'add'}),[])
	
	const handleButtonAlert = () =>{
		setTimeout(()=>{
			alert(count)
		},2000)
	}
	
	return (
		<div>
			<input value={name} onChange={e=>setName(e.target.value)} />
			<Child config={config} onButtonClick={handleButtonClick} />
			<button onClick={handleButtonClick}>alert</button>
		</div>
	)
}

const Child = meno(({onButtonClick,config}) =>{
	console.log('child render')
	return (
		<button onClick={onButtonClick} style={{color:config.color}} />
			{config.text}
		</button>
	)
})

# 执行结果说明：先点击两次button，此时count为3，然后点击alert按钮，之后再点击button，使count值为5，弹出的count为3
# 分析：当count为3，点击alert按钮的时候，handleButtonAlert内定义的延时计时器方法，形成一个闭包，保存了外部环境的count，同时，count是基础类型，所以值依旧为3，两秒后alert出的内容为3，而不是5；这样执行的结果实际上是正确的
# 场景，此时我们需要alert出的结果是5，该如何处理？
# 解决方式，通过保存的外部环境为引用类型，也就是对象来处理
const countRef = useRef()
countRef.current = count

const handleButtonAlert = () => {
	setTimeout(()=>{
		alert(countRef.current)
	},2000)
}

# 通过改成引用类型，对象的形式，保存的是同一个对象，但是值是最新的值；这样就能符合需求

```

