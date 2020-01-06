## Rxjs学习【深入浅出Rxjs】

#### 第一章	函数响应式编程

```
# 示例代码
# 场景：点击按钮放开，计算按住的时间
# JQuery代码
let startTime;

$('#btn').on('mousedown', () => {
    startTime = Date.now()
})

$('#btn').on('mouseup', () => {
    let result = Date.now() - startTime;
    $('#result').text(result + 'ms')
    startTime = null;
})

# Rxjs代码
const holdButton = document.getElementById('btn');
const mouseDown$ = Rx.Observable.fromEvent(holdButton, 'mousedown')
const mouseUp$ = Rx.Observable.fromEvent(holdButton, 'mouseup')

const holdTime$ = mouseUp$.timestamp().withLatestFrom(mouseDown$.timestamp(),(mouseUpEvent,mouseDownEvent)=>{
    return mouseUpEvent.timestamp - mouseDownEvent.timestamp;
})

holdTime$.subscribe(ms=>{
    document.getElementById('result').innerText = ms + 'ms';
})

# 流
# rxjs中一种特殊的对象，称之为‘流’
# 代表‘流’的变量标识符，都是用$符号结尾，这是Rxjs编程中普遍使用的风格，被称为“苏格兰命名法”

# 函数式编程
# 函数式编程对函数的使用有特殊的要求：
1.声明式
2.纯函数
3.数据不可变性

# 声明式：
# 一个函数作为参数传递给另一个函数

# 纯函数：
# 函数的执行过程完全由输入参数决定，不会受除参数之外的任何数据影响
# 函数不会修改任何外部状态，比如修改全局变量或传入的参数对象

# 数据的不可变性
# 不可变类型：字符串，数字类型（这种不可变是指，对其进行操作，类似arr.split这种）
# 通过产生新的数据，来代替对对现有数据的修改，保持原数据不变

# Reactive Extension
# 也叫ReactiveX，或者简称，Rx，指的是实践响应式编程的一套工具


```

#### 第二章	RxJS入门

```
# Rxjs的版本和运行环境
# 目前被广泛使用的Rxjs版本：v4和v5
# NPM安装
# v5
npm install rxjs 
# v4
npm install rx

# 建议导入方式
# 这样减少打包体积
# deep link
import {Observable} from 'rxjs/Observable'
# OR
const Observable = require('rxjs/Observable').Observable

# Tree-Shaking
# 指的是在打包过程中发现根本没有用上的函数，然后最终的打包文件也就不需要包含这些没被使用的函数代码。
# 示例：
# util.js
export const foo = () => 'foo'
export const bar = () => 'bar'

# main.js
import {foo} from 'util'
foo()
# 在有Tree-Shaking之前，打包文件会将util.js文件中的所有内容都放到打包文件中，因为main.js使用了util中定义的函数，很自然要把文件内容添加进来，有了tree-shaking之后，打包工具可以很聪明的发现bar函数定义虽然出现在util中，但是从来没有调用过，于是就会在最终的打包文件中删除bar的函数定义，从而减少打包文件的体积
# tree-shaking只对import语句导入产生作用，对于CommonJS的require函数导入方式不产生作用，因为tree-shaking的工作方式是对代码的静态分析，import只能出现在文件的开头，而require可以出现在任何位置，这样也就没法起到效果

# 最好的导入方式：
import {Observable} from 'rxjs/Observable'
import 'rxjs/add/observable/of'
# OR
require('rxjs/Observable').Observable
require('rxjs/add/observable/of')

Observable："可被观察者"
Observer："观察者"

# rxjs中的数据流就是Obserable对象，Observable实现了两种设计模式
1.观察者模式（Observer Pattern）
2.迭代器模式（Iterator Pattern）

# 观察者模式
# 角色：Publisher（发布者）、观察者（Observer）
# 观察者模式将复杂的问题分成了三个小问题
1.如何产生事件，这是发布者的责任，在Rxjs中是Observable对象的工作
2.如何响应事件，这是观察者的责任，在Rxjs中由subscribe的参数决定
3.什么样的发布者关联什么样的观察者，也就是何时调用subscribe

# 迭代器模式
# 在Rxjs中，作为迭代器的使用者，并不需要主动从Observable中‘拉’数据，而是只要subscribe上Observable对象之后，自然就能够收到消息的推送，这就是观察者模式和迭代器两种模式结合的强大之处

# 创造Observable
# Observable对象，代表的就是在一段时间范围内发生的一系列事件
# Observable=Publisher+Iterator

# 简单示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    observer.next(1)
    observer.next(2)
    observer.next(3)
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: item => console.log(item)
}

source$.subscribe(theObserver)
# 说明：
Observable对象也就是创建一个“发布者”，一个“观察者”调用某个Observable对象的subscribe函数，对应的onSubscribe函数就会被调用，参数就是“观察者”对象，onSubscribe函数中可以任意操作“观察者”对象
# 在Rxjs中，Observable是一个特殊类，它接受一个处理Observer的函数，而Observer就是一个普通的对象，没有什么神奇之处，对Observer对象的要求只有它必须包含一个名为next属性

# 跨越时间的Observable
# 示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    let number = 1;
    let timer = setInterval(() => {
        observer.next(number++)
        if (number > 3) {
            clearInterval(timer)
        }
    },1000)
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: item => console.log(item)
}

source$.subscribe(theObserver)
# 对于推数据的控制，放在observable，observer只需要关心对数据的处理即可

# 永无止境的Observable
# 示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    let number = 1;
    let timer = setInterval(() => {
        observer.next(number++)
    }, 1000)
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: item => console.log(item)
}

source$.subscribe(theObserver)
# 说明：
会一直每隔一秒吐出一个整数；假如我们不中断这个程序，让它一直运行下去，这个程序也不会消耗更多的内存，这是因为Observable对象每次只吐出一个数据，然后这个数据就被Observer消化处理了，不会存在数据的堆积

# Observable的完结
调用Observer的next方法只能表示“这是现在要推送的数据”，next没法表达“已经没有更多的数据了”，这样Observer依然时刻准备着接收数据，相关的资源无法被释放，所以需要在引入另一种机制，来告知Observer已经完成了
# Rxjs中，实现这种通信机制用的就是Observer的complete函数

# 示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    let number = 1;
    let timer = setInterval(() => {
        observer.next(number++)
        if (number > 3) {
            observer.complete()
            clearInterval(timer)
        }
    }, 1000)
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: item => console.log(item),
    complete: () => console.log("No Data")
}

source$.subscribe(theObserver)
# 即使observable继续向外吐数据，observer也不会再接收了

# Observable的出错处理
# 通过在Observable和Observer的交流渠道中增加一个error函数来实现
# 示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    observer.next(1)
    observer.error('something wrong')
    observer.complete()
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: item => console.log(item),
    complete: () => console.log("No Data"),
    error: (err) => console.error(err)
}

source$.subscribe(theObserver)
# 说明：在Rxjs中，一个Observable对象只有一种终结状态，要么是完结（complete），要么是出错（error），一旦进入出错状态，这个Observable对象也就终结了，再不会调用对应的next函数，也不会调用complete函数，同样如果一个Observable进入complete状态，也不能调用Obsever的next和error方法了；所以上面observer.complete()不会被调用

# Observer的简单形式
# 上面定义observer是通过对象的形式，其中包含next、error、complete方法，subscribe也可以直接接受函数作为参数，示例代码如下：
source$.subscribe(
	data => console.log(data),
	err => console.error(err),
	() => console.log('Complete')
)
# 说明：complete和error可以根据实际情况进行省略，但是顺序必须是next、error、complete，如果error省略，但是complete需要，则要通过null占位，但是建议为了程序的健壮性，最好还是带上error处理函数，如果真的发生意外造成错误，如果observer没有用error来接受异常，那么这个异常就会被系统接住，而系统接住异常的做法就是输出异常，然后程序退出

# 退订Observable
# 现在的场景是，Observable每秒往外吐出数据，但是observer只需要接收前3.5秒发送的数据，之后就退订消息
# 示例：
import { Observable } from 'rxjs/Observable'

const onSubscribe = observer => {
    let number = 1
    let timer = setInterval(() => {
        console.log('send:' + number)
        observer.next(number++)
    }, 1000)
    return {
        unsubscribe: () => {

        }
    }
}

const source$ = new Observable(onSubscribe)

const theObserver = {
    next: data => console.log('receive' + data),
    complete: () => console.log("No Data"),
    error: (err) => console.error(err)
}

const subscription = source$.subscribe(theObserver)

setTimeout(() => {
    subscription.unsubscribe();
}, 3500)

# 说明：Observable对象source$在unsubscribe函数调用之后依然在不断的吐数据，但是unsubcribe已经断开了source$对象和observer的连接，所以不再接收新的数据

# 备注：v5中对各个函数的命名进行改变，v4（dispose，onNext，onComplete，onError），v5（unsubscribe,next,complete,error），这样做的原因：有一个在javascript中实现observable类型的提案，提案中使用的函数名就是v5中所使用的函数名，v5的改变也是为了和未来javascript标准保持一致

# Hot Observable和 Cold Observable
# 场景：一个Observable有两个observer对象订阅，但是这两个不是同时订阅，第二个在第一个订阅之后，过了n秒后才订阅，在这n秒中内observable已经吐出部分的数据，此时：
A：错过就错过，从此刻接收后面新的数据
B：不能错过，需要获得Observable之前产生的数据
# 对于A，这样的Observable我们称为Hot Observable
# 对于B，这样的Observable我们称为Cold Observable
# 此刻我们需要引入新的角色--"生产者"，对于B我们可以理解为，每次subscribe的时候都产生了一个”生产者“，然后生产者产生的数据通过next传递给observer
# 示例代码：
const cold$ = new Observable(observer=>{
	const producer = new Producer()
	// 然后让observer去接受producer产生的数据
})
# 而对于Hot Observable，概念上是有一个独立于Observable对象的“生产者”，这个生产者的创建和subscribe调用没有关系，subscribe调用只是让Observer连接上“生产者”而已
# 示例代码
const producer = new Producer()
const hot$ = new Observable((observer)=>{
	// 然后让observer去接受producer产生的数据
})
# 在Hot Observable中，Observable明显并不生产数据，只是数据的搬运工
# 一个Observable是Hot还是Cold，都是相对于生产者而言的

# 操作符简介
# 对于现实中复杂的问题，并不会通过new Observable直接创建数据流，之后subscribe接上一个Observer这么简单，往往需要对数据流做一系列处理，然后才交给observer，就像一个管道，管道的各个环节都会对数据做处理
# 在Rxjs中，组成数据管道的元素就是操作符
# 对于操作符，链接的就是上游（upstream）和下游（downstream）
# 在Rxjs中，有一系列用于产生Observable函数，这些函数有的凭空创造Observable对象，有的根据外部数据源产生Observable对象，更多的是根据其他的Observable中的数据来产生新的Observable对象，也就是把上有数据转化为下游数据，所有这些函数统称为操作符

# 弹珠图--学习Rxjs的利器
# 水平方向代表时间间隔，纵向的多个图表示原数据流和处理后的数据流对比

```

#### 第三章	操作符基础

```
# 操作符基础
# 定义：一个操作符是返回一个Observable对象的函数，不过，有的操作符是根据其他Observable对象产生返回的Observable对象，有的操作符则是利用其他类型输入产生返回的Observable对象，还有一些操作符不需要输入就可以凭空创造一个Observable对象

# 操作符的分类
1.创建类
2.转化类
3.过滤类
4.合并类
5.多播类
6.错误处理类
7.辅助工具类
8.条件分支类
9.数学和合计类

# 静态和实例分类
# 示例：javascript创建一个类
function Programmer(){}

// 通过静态方式添加方法
Programmer.create = function(){
	return new Programmer()
}

// 给实例添加方法
Programmer.prototype.code = function(){
	return 'hello world'
}

# 所有的操作符都是函数，不过有的操作符是Observable类的静态函数，也就是不需要Observable实例就可以执行的函数，所以称为“静态操作符”；另一类操作符是Observable的实例函数，前提是要有一个创建好的Observable对象，这一类称为“实例操作符”

# 对于静态操作符，比如of，引入方式：
import 'rxjs/add/observable/of'
# 调用方式
const source$ = Observable.of(/*具体参数*/)

# 对于实例操作符，比如map，引入方式：
import 'rxjs/add/operator/map'
# 调用方式
const result$ = source$.map(/*具体处理*/)

# 同时有些功能既可以作为Observable对象的静态方法，也可以作为Observable对象的实例方法，比如merge这个合并操作符
import 'rxjs/add/observable/merge'
const result$ = Observable.merge(source$,source2$)
# OR
const result$ = source$.merge(source2$)

# 操作符函数的实现
# 每个操作符都是一个函数，必须满足以下功能要点
1.返回一个全新的Observable对象
2.对上游和下游的订阅及退订处理
3.处理异常情况
4.及时释放资源

# 以下是一步一步的实现方式，并填充新的知识点

# 返回一个全新的Observable对象
# 示例：
import { Observable } from "rxjs/Observable"
import 'rxjs/add/observable/of'

function map(project) {
    return new Observable((observer) => {
        this.subscribe({
            next: value => observer.next(project(value)),
            error: err => observer.error(err),
            complete: () => observer.complete()
        })
    })
}

// 添加实例方法
Observable.prototype.map = map;

const source$ = Observable.of('1', '2', '3').map(x => x)

source$.subscribe(console.log, console.error, () => {
    console.log('no data')
})
# 说明：这里this代表的是上游的Observable对象，所以可以直接subscribe订阅

# 订阅和退订处理
# 场景：对于map，并没有占用什么资源，但是无法知道上游的observable是如何实现的，上游完全可能分配了特殊的资源，这样当下游退订时，我们需要让上游感知到退订动作
# 示例：
import { Observable } from "rxjs/Observable"

function map(project) {
    return new Observable((observer) => {
        const sub = this.subscribe({
            next: value => observer.next(project(value)),
            error: err => observer.error(err),
            complete: () => observer.complete()
        })
        return {
            unsubscribe: () => {
                sub.unsubscribe()
            }
        }
    })
}

Observable.prototype.map = map;

const source$ = new Observable((observer) => {
    let number = 1
    setInterval(() => {
        console.log('send:' + number)
        observer.next(number++)
    }, 1000);
}).map(x => x)

const result = source$.subscribe((data) => {
    console.log('receive:' + data);
}, console.error, () => {
    console.log('no data')
})

setTimeout(() => {
    result.unsubscribe()
}, 3500)

# 基本原则就是：当不再需要从某个Observable对象获取数据的时候，就要退订这个Observable对象

# 处理异常情况
# 为什么要这样做
1.编写健壮的程序代码
2.对于以上示例来说，project是一种输入，不受map自身控制，换句话说project可能是有问题的代码，这就超出了map函数的控制范围
# 处理方式：捕获异常错误，把异常错误沿着数据流分发给下游
# 示例：
function map(project) {
    return new Observable((observer) => {
        const sub = this.subscribe({
            next: value => {
                try {
                    observer.next(project(value))
                } catch (err) {
                    observer.error(err)
                }
            },
            error: err => observer.error(err),
            complete: () => observer.complete()
        })
        return {
            unsubscribe: () => {
                sub.unsubscribe()
            }
        }
    })
}

# 关联Observable
# 场景：将自定义的map函数和Observable关联起来
# 1.给Observable打补丁
Observable.prototype.map = map
# 注：不能写成以下形式
Observable.prototype.map = (project)=>{
	// 在这里this并不是Observable对象本身
}
# 原因：尖头函数中的this直接绑定与定义函数环境下的this，而不是执行环境时指定的this，这样函数体中的this永远时函数外部的this，执行时，函数体通过this是无法访问到上游的Observable对象的

# 2.使用bind绑定特定的Observable对象
# 场景：以上绑定map操作符是影响了所有的Observable对象，但是有时候，我们只想对特定的Observable对象应用，比方说上面的map函数，Rxjs中本身定义了一个map操作符，如果通过上面的绑定方式，会造成覆盖了原有的map操作符实现，这个不是想要的结果
# 在调用的时候通过bind绑定特定的Observable对象
const result$ = map.bind(source$)(x=>x*2)
# 缺点：就是上游的Observable对象只能作为操作符函数的参数，这样就没法用链式调用，比方说连续使用两个map
const result$ = map.bind(map.bind(source$)(x=>x))(x=>x*2)
# 修改：
# 通过“绑定操作符”
const result$ = source$::map(x=>x*2)::map(x=>x)
# 注：babel需要引入插件实现代码转换

# 使用lift
# lift的功能是把Observable对象提升一个层级，赋予更多功能。lift是Observable的实例函数，它会返回一个新的Observable对象。
# 示例：
function map(project) {
    return this.lift((source$) => {
        return source$.subscribe({
            next: value => {
                try {
                    observer.next(project(value))
                } catch (err) {
                    observer.error(err)
                }
            },
            error: err => observer.error(err),
            complete: () => observer.complete()
        })
    })
}

# 改进的操作符定义
# 在之前的操作符实现方式中，操作符的函数实现部分都会用上this，如果严格按照函数式编程的思想，应该尽量使用纯函数，纯函数的执行结果应该完全由输入参数决定，函数实现中用了this，这就不能算是真正的纯函数。
# 新的定义操作符的方式：pipeable操作符，也称为lettable操作符

# 操作符和Observable关联的缺陷
# Rxjs中任何一个操作符都是挂在Observable类上或者Observable.prototype上的，赋值给Observable或者Observable.prototype上某个属性在Tree-Shaking看来就是被引用，所以，所有的操作符，不管真实运行时是否被调用，都会被Tree-Shaking认为是会用的代码，增大包的体积

# 不再通过以下方式导入：
import { Observable } from "rxjs/Observable"
import 'rxjs/add/observable/of'
# 而是通过以下方式导入：
import {of} from 'rxjs/observable'

# 以前的使用方式
const source$ = Observable.of(1,2,3)
# 现在的使用方式
import {of} from 'rxjs/observable/of'
const source$ = of(1,2,3)
source$.subscribe(console.log)

# lettable/pipeable操作符
# lettable和pipeable关系，只是管道更形象，作用和意义是一样的，都可以调用
# let调用方式
import { Observable } from "rxjs/Observable"
import 'rxjs/add/operator/let'

function map(project) {
    return function (obs$) {
        return new Observable((observer) => {
            const sub = obs$.subscribe({
                next: value => {
                    try {
                        observer.next(project(value))
                    } catch (err) {
                        observer.error(err)
                    }
                },
                error: err => observer.error(err),
                complete: () => observer.complete()
            })
            return {
                unsubscribe: () => {
                    sub.unsubscribe()
                }
            }
        })
    }

}

const source$ = new Observable((observer) => {
    let number = 1
    setInterval(() => {
        console.log('send:' + number)
        observer.next(number++)
    }, 1000);
})

const result = source$.let(map(x => x * 2)).subscribe((data) => {
    console.log('receive:' + data);
}, console.error, () => {
    console.log('no data')
})

setTimeout(() => {
    result.unsubscribe()
}, 3500)

# let的作用是把map函数引入到链式调用中，let起到连接上下游的作用，真正的工作由map函数执行；当let被调用的时候，实际上就是把上游的Observable对象作为参数传递给map，然后把返回的Observable交给下游来订阅；这样消除了this，满足了函数式编程的要求

# 但是使用let还是需要再引入，所以后来Rxjs让Observable类自带了一个新的操作符，名叫pipe，无需导入
# 对比let和pipe
const result$ = $source.pipe(
	map( x => x * 2 ),
	map( x => x + 2 )
)
const result$ = $source.let(map(x=>x*2)).let(map(x=>x+2))

# 管道操作符
# 同上面绑定操作符一样都是提议
# |> 用来连接两个值，前一个值没有任何要求，后一个值必须是函数，管道操作符的作用就是把前面的值作为参数来调用后面的函数
# 示例：
666 |> foo
=> foo(666)
666 |> foo |> bar
=> bar(foo(666))
# 这样函数的执行顺序和前后顺序一致，利于理解

```

#### 第四章	创建数据流

```
# Rxjs的世界中一切都以数据流为中心，在代码中，数据流以Observable实例对象的形式存在

# 创建类操作符
# 所谓创建类操作符，就是一些能够创造出Observable实例对象的方法，所谓的创造并不只是说返回一个Observable对象，因为任何操作符都会返回Observable对象，这里说的创造，是指这些操作符不依赖于其他Observable对象，可以凭空或者根据其他数据源创造一个Observable对象（数据源可以是DOM事件，websocket数据，ajax数据等）

# new Observable(参数函数)
# 这种创建方式定制化程度很高，但是对于现实中场景可以抽象出各种模式，通过模式（其他操作符或者其他操作符的组合）就可以解决问题
# =============================
# 同步数据流（无时间线，数据之间只有顺序，无时间间隔）

# create 毫无用处的操作符
# 具体实现形式：
Observable.create = (subscribe) =>{
	return new Observable(subscribe)
}

# =============================

# of 列举数据
# 示例：
import {Observable} from 'rxjs'
import 'rxjs/add/observable/of'
const source$ = Observable.of(1,2,3)
# OR
import {of} from 'rxjs/observable/of'
const source$ = of(1,2,3)

# =============================

# range 指定范围
# 推连续数列
# 示例：
Observable.range(1,100) 参数1：其实数值。参数2:数列个数
# 输出：1，2，3，4...98，99，100

# =============================

# generate 循环创建
# 最大限度定制每个值，类似for循环定制
# 比10小的所有偶数的平方
const source$ = Observable.generate(
	2, // 初始值，相当于for循环中的 i = 2
	value => value < 10, // 继续的条件，相当于for中的条件判断
	value => value + 2, // 每次值的递增
	value => value * value // 产生的结果
)
# 除了第一个参数为值外，其他三个参数都是函数，应该保持这三个参数都是纯函数

# =============================

# repeat 重复数据的数据流
# 重复指定次数订阅Observable对象
# 示例:
const source$ = Observable.of(1,2,3)
const repeat$ = source$.repeat(10)
# 说明：重复订阅source$ 10次，注：动作包含订阅和退订，并且是上游Observable对象完结之后才会重新订阅，因为在完结之前，repeat也不知道会不会有新的数据从上游推送下来，要保证上游最终一定会完结
# 参数，表示重复的次数，如果不传或者为负数，则会一直重复订阅，得到无限循环的数据流

# =============================

# empty、never和throw 极简操作符
# 当需要默认立即完结，立即出错或者永不完结的数据流对象时，三个操作符可以直接使用
Observable.empty()
Observable.never()
Observable.throw()
# 注throw单独引入的时候，记得使用_throw，否则会和关键字throw冲突
import {_throw} from 'rxjs/observable/throw'

# =============================

# 创建异步数据的Observable对象

# interval和timer：定时产生数据
# interval接受一个数值类型的参数，表示间隔时间（ms），从0开始，间隔指定时间输出值，每次递增+1
import 'rxjs/add/observable/interval'
const source$ = Observable.interval(1000)

# timer的第一个参数可以是一个数值，也可以是一个Date类型的对象，如果是数值，代表毫秒数，产生的Observable对象在指定的时间间隔之后吐出0，然后立即结束
# 示例：
Observable.timer(1000)
# 使用Date对象实现上面的示例
const now = new Date();
const later = new Date(now.getTime() + 1000)
const source$ = Observable.timer(later)
# 可以传入具体的Date对象，如果指定的时间未到，那么就会等到时间之后执行，如果时间已经过去，则会立即执行
# 示例：
import { Observable } from 'rxjs'
import 'rxjs/add/observable/timer'
import moment from 'moment'

// const source$ = Observable.timer(1000);
const target = moment('2019-10-14 17:50:50')
const source$ = Observable.timer(target.toDate())

source$.subscribe((data) => console.log('first', data), null, () => console.log('complete'))

// setTimeout(() => {
//     console.log(new Date())
//     source$.subscribe(console.log, null, () => console.log('complete'))
// }, 5000)

# timer还支持第二个参数，如果使用第二个参数，那么就会产生一个持续吐出数据的Observable对象，类似interval，第二个参数表示间隔时间，同时数值+1
# 以下两行代码执行效果相同
const source$ = Observable.interval(1000)
const source$ = Observable.timer(1000,1000)

# =============================

# from 可把一切转化为Observable
# 创造类操作符中包容性最强的，它接受的参数只要“像”Observable就可以
# 例如：数组，字符串（把单个字符拆分），generator，promise，即使是对象，只要表现的像是一个数组，一眼可以被from转化

# =============================

# fromPromise 异步数据的交接
# fromPromise只能接受Promise对象作为参数
# 示例：
const promise = Promise.resolve('good')
const source$ = Observable.fromPromise(promise)

source$.subscribe(
	console.log,
	error=>console.error(error),
	()=>console.log('complete')
)

# 运行结果：
good
complete

# 示例：
const promise = Promise.reject('fail')
const source$ = Observable.fromPromise(promise)

source$.subscribe(
	console.log,
	error=>console.error(‘catch’,error),
	()=>console.log('complete')
)

# 运行结果：
catch fail

# =============================

# fromEvent
# fromEvent最常见的用法就是把DOM中的事件转化为Observable对象中的数据
# formEvent([事件源，对于浏览器，指的是特定的DOM元素，对于node，指的是事件发生器实例],[事件名称])
# 示例：
# 浏览器
const event$ = Rx.Observable.fromEvent(document.getElementById('btn'),'click')
event$.subscribe((data)=>console.log(data))
# node
import 'rxjs/add/observable/fromEvent'
import EventEmitter from 'events'

const emitter = new EventEmitter()
const source$ = Observable.fromEvent(emitter, 'msg')

emitter.emit('msg', 'demo')

source$.subscribe(
    console.log,
    null,
    () => console.log('complete')
)

emitter.emit('msg','haha')
emitter.emit('another-msg','woqu')
emitter.emit('msg','lihai')

# 运行结果：
haha
lihai
# 并且emit动作要在source$.subscribe() 发生才能被接收到
# 因为fromEvent产生的是Hot Observable，也就是数据的产生与订阅是无关的，如果在订阅之前调用emitter.emit，那有没有Observer这些数据都会立刻吐出来，等不到订阅的时候，当添加Observer的时候，自然什么数据都获得不到

# =============================

# fromEventPattern
# 比fromEvent更高的灵活性
# fromEventPattern接收两个函数参数，分别对应产生Observable对象被订阅和退订的动作，因为两个参数是函数，所以可以非常灵活
# 示例：
import { Observable } from 'rxjs/Observable'
import 'rxjs/add/observable/fromEventPattern'
import EventEmitter from 'events'

const emitter = new EventEmitter()

const addHandler = (handler) => {
    emitter.addListener('msg', handler)
}

const removeHandler = (handler) => {
    emitter.removeListener('msg', handler)
}
// 对应调用addHandler
const source$ = Observable.fromEventPattern(addHandler, removeHandler);

const subscription = source$.subscribe(
    console.log,
    error => console.error(error),
    () => console.log('complete')
)

emitter.emit('msg', 'hello')
emitter.emit('msg', 'world')

// 对应调用removeHandler操作
subscription.unsubscribe()
emitter.emit('msg', 'demo')

# 注：存在缺陷，没有对应的error和complete调用

# =============================

# ajax
# 获取远程数据资源
# 示例：场景获取Rxjs项目的start数量
Rx.Observable.fromEvent(
	document.getElementById('btn')
).subscribe(()=>{
	Rx.Observable.ajax('https://api.github.com.com/repos/ReactiveX/rxjs',{responseType:'json'}).
	subscribe(value=>{
		const starCount = value.response.stargazers_count
	})
})

# =============================

# repeatWhen
# repeat能反复订阅上游的Observable，但是不能控制订阅时间，比如希望在接收到上游完结事件的时候等待一段时间再重新订阅，repeatWhen能够实现

# repeatWhen接受一个函数作为参数，这个函数在上游第一次产生异常时被调用，然后这个函数应该返回一个Observable对象，这个对象就是一个控制器，当这个Observable对象吐出第一个数据时候，repeatWhen就会做退订上游并重新订阅的动作

# 当上游数据是同步数据的时候
# 示例：
const notifier =()=>{
	return Observable.interval(1000)
}
const source$ = Observable.of(1,2,3)
const repeat$ = source.repeatWhen(notifier)

# 当上游数据是异步数据的时候
# 示例：
const notifier =(notification$)=>{
	return notification$.delay(2000)
}
const repeat$ = source$.repeatWhen(notifier)
# 书写形式如上

# repeatWhen中我们用notifier返回的Observable对象来控制节奏，但是，其实这个Observable里每次吐出来的数据是什么值没有什么关系，因为我们只是需要这个对象吐出第一个数据的时机，从而来控制时间节奏

# =============================

# defer
# 场景：有时候创建一个Observable的代价不小，所以我们希望能够尽量延迟对应的Observable的创建，但是从方便的角度，我们又希望有一个Observable预先存在，这样能够方便订阅
# 具体是，我们能够提前创建Observable对象，但是不分配资源，直到数据流被订阅了再正式分配资源
# 示例：
import 'rxjs/add/observable/defer'
import 'rxjs/add/observable/of'

const observableFactory =()=>Observable.of(1,2,3)
const source$ = Observable.defer(observableFactory)
# 说明：source$被创建出来的时候，并没有任何实际数据的Observable产生，只有当它被订阅的时候，observableFactory就会被调用，这时候通过of产生的数据流才被创造出来作为真正的数据源

# ajax示例
const observableFactory =()=>Observable.ajax(ajaxUrl)
const source$ = Observable.defer(observableFactory)


```

#### 第五章	合并数据流

```
# 合并数据流
# 这类操作符都有多个Observable对象作为数据来源，把不同来源的数据根据不同的规则合并到一个Observable对象
# 不少合并类操作符都有两种形式，既提供静态操作符，又提供实例操作符。要根据当时的使用环境，确定采用的形式，例如：有source1$和source2$,如果语义上是source$2汇入source1$，那么用实例操作符更加符合语义，如果场景上两个数据流之间没有主从关系，只是平等关系，这时候用静态操作符更加符合语义

# concat
# 特征：
1.首尾相连（从第一个Observable对象获取数据，把数据传给下游，当第一个Observable对象complete之后，会去订阅第二个Observable对象，以此类推）
2.参数Observable对象的数量是不做限制的
3.依次订阅，上一个退订之后再去订阅下一个
# 示例：
// 静态方法
import 'rxjs/add/observable/concat'

const source1$ = Observable.of(1, 2, 3)
const source2$ = Observable.of(4, 5, 6)
const concat$ = Observable.concat(source1$, source2$)
concat$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

// 实例方法
import 'rxjs/add/operator/concat'

const source1$ = Observable.of(1, 2, 3)
const source2$ = Observable.of(4, 5, 6)
const concat$ = source1$.concat(source2$)
concat$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# =============================

# merge
# 特征：
1.先到先得快速通过（第一时间订阅所有上游Observable对象，任何一个上游Observable对象吐出数据，就立即转到下游）
2.直到所有的上游Observable都complete，下游才complete
3.merge可选参数，控制可以同时合并的Observable对象的个数，说明：source1$.merge(source2$,source3$,2)，限制了可同时合并的个数为2，source3$只有在source2$或source1$中任意一个完结的时候，才会空缺出一个名额给source$3，否则soure$3是没有合并的机会的
# 示例：
// 静态方法
import 'rxjs/add/observable/merge'

const source1$ = Observable.of(1, 2, 3)
const source2$ = Observable.of(4, 5, 6)
const merge$ = Observable.merge(source1$, source2$)
merge$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

// 结果1，2，3，4，5，6  由于订阅顺序，先订阅source$1，后订阅source2$，并且是同步吐出数据，所以是这个结果

// 实例方法
import 'rxjs/add/observable/timer'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/merge'

const source1$ = Observable.timer(1000, 1000).map(x => x * 100)
const source2$ = Observable.timer(1000, 1000).map(x => x + 1)
const merge$ = source1$.merge(source2$)
merge$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：0，1，100，2，200，3  异步数据也是按照订阅先后吐出数据

# 注：应该避免用merge去合并同步数据流，merge应该用于合并产生异步数据的Observable对象，一个常用的场景就是合并DOM事件

# =============================

# zip 拉链式组合
# 特征：
1.一对一的合并，默认返回数据格式为数组，上游数据流中对应顺序的数据组合成数组形式传给下流，时机按照对应顺序的数据组合完成的时机，例如：一个是同步传输数据，一个是每秒吐出一个数据，那么下流接收到数据的时机是按照最慢的每秒传递一次数据数组，zip要严格按照一一对应来吐出数据
2.结束时机：任何一个上游的Observable完结，zip只要给这个完结的Observable对象吐出的所有数据找到配对的数据，那么zip就会给下游传递complete信号，zip在完结的时候会退订所有的上游数据流
3.zip多个数据流，对上游数据流数量没有限定，complete的时机还是一样的，所以吐出数据的个数由上游数据最少的Observable决定

# 示例：
// 静态方法
import 'rxjs/add/observable/zip'
import 'rxjs/add/observable/of'

const source1$ = Observable.of(1,2,3)
const source2$ = Observable.of('a','b','c')
const source3$ = Observable.of('A','B','C')
const zip$ = Observable.zip(source1$,source2$,source3$)
zip$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ 1, 'a', 'A' ]
[ 2, 'b', 'B' ]
[ 3, 'c', 'C' ]
complete

// 实例方法
import 'rxjs/add/observable/timer'
import 'rxjs/add/observable/of'
import 'rxjs/add/operator/zip'
import 'rxjs/add/operator/map'

const source1$ = Observable.of(1,2,3,4,5)
const source2$ = Observable.timer(1000,1000).map(x=>`${x}b`)
const zip$ = source1$.zip(source2$)
zip$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ 1, '0b' ]
[ 2, '1b' ]
[ 3, '2b' ]
[ 4, '3b' ]
[ 5, '4b' ]
complete

# =============================

# combineLatest：合并最后一个数据
# 特征：
1.合并最新的数据（任何一个上游产生数据时，从所有输入的Observable对象中拿最后一次产生额数据（最新数据），然后把数据组合起来传给下游。注：所有输入的Observable对象，必须是已经吐出值的那种上游数据流，只要有一个上游数据源还没有产生数据，那么就只能等待，因为凑不齐返回的数据；传给下流的数据默认是数组形式的）
2.完结的时机，只有所有的上游Observable对象都完结之后，combineLatest才会给下游一个complete信号
3.如果上游数据源产生的数据都是同步的，由于订阅的顺序造成结果不一样，如下
# 示例代码：
import 'rxjs/add/observable/timer'
import 'rxjs/add/operator/map'
// 静态方法
import 'rxjs/add/observable/combineLatest'

const source1$ = Observable.timer(0, 1000)
const source2$ = Observable.timer(500, 1000)
const combineLatest$ = Observable.combineLatest(source1$, source2$)
combineLatest$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ 0, 0 ]
[ 1, 0 ]
[ 1, 1 ]
[ 2, 1 ]
[ 2, 2 ]
[ 3, 2 ]

// 实例方法
import 'rxjs/add/operator/combineLatest'

const source1$ = Observable.of(1, 2, 3)
const source2$ = Observable.of('a', 'b', 'c')
const combineLatest$ = source1$.combineLatest(source2$)
combineLatest$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ 3, 'a' ]
[ 3, 'b' ]
[ 3, 'c' ]
complete

# 多重依赖的问题
# 示例代码：
import 'rxjs/add/operator/map'
import 'rxjs/add/observable/combineLatest'

const source1$ = Observable.timer(0, 1000).map(x => x * 100)
const source2$ = Observable.timer(0, 1000).map(x => x + 1)
const combineLatest$ = Observable.combineLatest(source1$, source2$)
combineLatest$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ 0, 1 ]
[ 100, 1 ]
[ 100, 2 ]
[ 200, 2 ]
[ 200, 3 ]
[ 300, 3 ]
[ 300, 4 ]
# 注：每次触发抛出两个值，后一个是正常的
# 注：这种现象称为小缺陷（glitch），指的是combineLatest这样的操作符输出的不一致情况，glitch发生是因为多个上游Observable“同时”吐出一个数据，解决这个缺陷，使用withLatestFrom操作符

# =============================

# withLatestFrom
# 特征：
1.传给下游数据的时机只能由一个上游Observable对象驱动
2.withLatestFrom操作符只有实例操作符的形式，而且所有输入Observable的地位并不相同，调用withLatestFrom的那个Observable对象起到主导数据产生节奏的作用，作为参数的Observable的对象只能贡献数据，不能控制产生数据的时机

# 示例代码：
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/withLatestFrom'

const source1$ = Observable.timer(0, 1000).map(x => `${x}a`)
const source2$ = Observable.timer(0, 1000).map(x => `${x}b`)
// source1$控制数据的输出时机
const withLatestFrom$ = source1$.withLatestFrom(source2$)
withLatestFrom$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
[ '0a', '0b' ]
[ '1a', '1b' ]
[ '2a', '2b' ]
[ '3a', '3b' ]

# 解决glitch的关键是我们知道输出数据的节奏只需要和其中一个输入Observable对象一致就足够

# =============================

# race：胜者通吃
# 特征：
1.第一个吐出数据的Observable对象为胜者，race产生的Observable会完全采用胜者Observable中的数据，并且退订其他的Observable对象
# 示例代码：
import 'rxjs/add/observable/race'
import 'rxjs/add/operator/map'

const source1$ = Observable.timer(0, 1000).map(x => `${x}a`)
const source2$ = Observable.timer(500, 1000).map(x => `${x}b`)
const race$ = Observable.race(source2$, source1$)
race$.subscribe((val) => {
    console.log(val)
}, null, () => console.log('complete'))

# 结果：
0a，1a，2a，3a，4a

# =============================

# startWith
# 特征：
# startWith只有实例操作符的形式，其功能是让一个Observable对象在被订阅的时候，总是先吐出指定的若干个数据
# 示例代码：
import 'rxjs/add/operator/startWith'

const source1$ = Observable.of(1, 2, 3)
const startWith$ = source1$.startWith('demo', 'haha')
startWith$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：demo，haha，1，2，3，complete

Observable.timer(0, 1000).map(x => `${x}a`).startWith('demo', 'haha').subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：demo，haha，0a，1b ....

Observable.timer(0, 1000).map(x => x * 2).concat(Observable.of('end')).map(x => x + 'ok').take(2).subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：0ok，2ok，complete

# =============================

# forkJoin
# 特征：
1.forkJoin只有静态操作符的形式
2.forkJoin只会产生一个数据
3.forkJoin只有在所有的Observable参数都complete之后，才会将所有Observable参数最后产生的一个数据打包成数组返回出来
# 示例代码：
import 'rxjs/add/observable/forkJoin'

const source1$ = Observable.of(1, 2, 3)
const source2$ = Observable.of('a', 'b', 'c')
const source3$ = Observable.of('A', 'B', 'C')

Observable.forkJoin(source1$, source2$, source3$).subscribe(
    (value) => console.log(value),
    null,
    () => console.log('complete')
)

# 结果：[ 3, 'c', 'C' ]，complete

# =============================

# 高阶Observable（Higher Order Observable）
# 高阶Observable依然是一个Observable，只是形式比较特殊；指的是产生的数据依然是Observable的Observable
# 示例：
const ho$ = Observable.interval(1000)
							.take(2)
							.map(x=>Observable.interval(1500).map(y=>x+':'+y).take(2))
							
# 高阶Observable产生的数据，一般称为内部Observable，因为它们一般也不展示在外
# 内部的Observable并不会随主干Observable的完结而完结，作为独立的Observable，有自己独立的生命周期
# 高阶Observable的本质是用管理数据的方式管理多个Observable对象
# 操作高阶Observable的合并操作符：concatAll，mergeAll，zipAll，combineAll

# =============================

# concatAll
# 特征：
1.将高阶Observable的所有内部Observable进行concat处理，产生一个独立的Observable
2.同concat一样，只有在第一个内部Observable完结之后才会订阅第二个内部Observable
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/concatAll'

const ho$ = Observable.interval(1000).map(x => Observable.interval(1500).map(y => `${x}:${y}`).take(2)).take(2)
const concat$ = ho$.concatAll()
concat$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：
0:0
0:1
1:0
1:1
complete

# =============================

# mergeAll
# 特征：
1.同merge，只是订阅的对象是高阶Observable的内部Observable，一旦高阶Observabel产生一个内部Observable，就会立即去订阅，并从中抽取数据，具体产生的数据按照时间线来判断，同样是吐出内部Observable产生的最新数据
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/mergeAll'

const ho$ = Observable.interval(1000).map(x => Observable.interval(1500).map(y => `${x}:${y}`).take(2)).take(2)
const mergeAll$ = ho$.mergeAll()
mergeAll$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：
0:0
1:0
0:1
1:1
complete

# =============================

# zipAll
# 特征：
1.同zip，只是订阅的对象是高阶Observable产生的内部Observable，
2.zip操作必须要确定有多少Observable，这样才能操作，所以要使zipAll工作，首先上游的高阶Observable必须完结，只有完结之后才能确定内部Observable数量，进行zip操作，如果上游高阶Observable不完结，那么zipAll就不会开始工作
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/zipAll'

const ho$ = Observable.interval(1000).map(x => Observable.interval(1500).map(y => `${x}:${y}`).take(2)).take(2)
const zipAll$ = ho$.zipAll()
zipAll$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果：
[ '0:0', '1:0' ]
[ '0:1', '1:1' ]
complete

# =============================

# combineAll
# 特征：
1.同combineLatest，只是订阅的对象是高阶Observable产生的内部Observable
2.必须上游高阶Obsrvable完结之后才能开始给下游产生数据，因为只有确定了作为输入的内部Observable对象的个数，才能拼凑出第一个传给下游的数据
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/combineAll'

const ho$ = Observable.interval(1000).map(x => Observable.interval(1500).map(y => `${x}:${y}`).take(2)).take(2)
const combineAll$ = ho$.combineAll()
combineAll$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果
[ '0:0', '1:0' ]
[ '0:1', '1:0' ]
[ '0:1', '1:1' ]
complete

# =============================

# 进话的高阶Observable处理
# 场景：concatAll存在一个问题，当上游的Observable产生Observable对象的速度过快，快过内部Observable产生数据的速度，并且concatAll要做无损的数据流链接，这样就会造成数据积压，实际上很多场景并不需要无损的数据流链接，也就是说可以舍去一些数据，对于如果进行舍去就要引入switch和exhaust这两个操作符

# =============================

# switch
# 特征：
1.总是切换到最新的内部Observable对象获取数据，具体：当上游高阶Observable产生一个新的内部Observable对象，switch就会订阅最新的这个内部Observable对象，如果之前订阅了旧的内部Observable，此时就会作退订操作，然后订阅新的内部Observable
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/switch'

const ho$ = Observable.interval(1000).map(x => Observable.interval(1500).map(y => `${x}:${y}`).take(2)).take(2)
const switch$ = ho$.switch()
switch$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果
1:0
1:1
complete

# 注：switch产生的Observable对象何时完结，基于两个条件
1.上游高阶Observable已经完结
2.当前内部Observable已经完结

# =============================

# exhaust
# 特征：
1.在耗尽当前内部Observable的数据之前不会切换到下一个内部Observable对象
2.在时间线上进行描述：最开始上游高阶Observable产生第一个内部Observable的时候开始订阅，之后一直传给下游当前内部Observable生产的数据，直到当前内部Observable完结（complete），之后会订阅下个当前时间之后产生的内部Observable对象，再去消耗这个Observable上的数据，如果在上一个内部Observable完结之前，上游高阶Observable产生了新的内部Observable，exhaust会对其进行忽略，只会订阅上个内部Observable完结之后产生的新的内部Observable对象
# 示例代码：
import 'rxjs/add/observable/interval'
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/map'
import 'rxjs/add/operator/exhaust'

const ho$ = Observable.interval(1000).map(x => Observable.interval(700).map(y => `${x}:${y}`).take(2)).take(3)
const exhaust$ = ho$.exhaust()
exhaust$.subscribe((value) => console.log(value), null, () => console.log('complete'))

# 结果
0:0
0:1
2:0
2:1
complete

# 注：exhaust产生的Observable对象何时完结，基于两个条件
1.上游高阶Observable已经完结
2.当前内部Observable已经完结

```

#### 第六章	辅助类操作符

```
# 辅助类操作符

# 数学类操作符
# 特征：
1.体现数学计算功能的一类操作符
2.所有操作符都是实例操作符
3.这些操作符必定遍历完上游Observable吐出的所有数据后才会向下游传递数据

# count 统计数据个数
# 统计上游Observable吐出所有数据的个数
# 示例代码:
import 'rxjs/add/operator/count'
const count$ = Observable.of(1, 2, 3).count()
count$.subscribe(
    (value) => console.log(value), 
    (error) => console.error(error), 
    () => console.log('complete')
)

# =============================

# max/min 最大值/最小值
# 用法相同，一个是取上游Observable吐出的所有数据最大值，一个是取最小值
# 如果是数值，比较大小，如果是复杂的数据类型，那必须指定比较的方法
# 比较大小的函数
function (a,b){
	// 如果a和b相等则返回0，如果a>b返回正数，如果a<b返回负数
}
# 示例代码：
// max/min
import 'rxjs/add/operator/min'
import 'rxjs/add/operator/max'

const min$ = Observable.of(1, 2, 3).min()
min$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

const max$ = Observable.of(1, 2, 3).max()
max$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

const complex$ = Observable.of(
    { name: 'enen', age: 23 },
    { name: 'haha', age: 15 },
    { name: 'hehe', age: 24 }
).max((a, b) => a.age - b.age)
complex$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# reduce 规约统计
# 同reduce函数，对一个集合中的所有元素依次调用规约函数，返回一个累积的结果，reduce可选参数seed（初始值），如果不指定seed，那么数据集合的第一个数据充当初始值
# 示例代码：
import 'rxjs/add/operator/reduce'
const reduce$ = Observable.range(1, 100).reduce((prev, cur, index) => prev + cur)
reduce$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# 条件布尔类操作符
# 特征：
1.根据上游Observable对象的某些条件产生一个新的Observable对象，并涉及“判定函数”概念
2.“判定函数”的功能就是判定输入的参数是否满足某个条件
function isEven(value,index,source$){
	return value % 2 === 0
}

# every 判断上游Observable吐出的数据是否都满足判定函数
# 特征：
1.如果经过判定函数检验结果都是为true，则上游Observable完结之后，创建新的Observable对象并吐出唯一的数据true，反之，如果上游的数据在检验的过程中出现了任意一个结果false，那么不需要等上游Observable完结，every会产生Observable对象，并立即吐出false
# 示例代码：
// every
import 'rxjs/add/operator/every'
const every$ = Observable.timer(0, 1000).every((value) => {
    console.log(value)
    return value < 3
})
every$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 注：并不是等上游的Observable完结了才执行every，上游每次吐出数据就会用every作判断

# =============================

# find/findIndex
# 特征：
1.同js中的find 和 findIndex方法一样，find返回找到的符合条件的第一个数据，findIndex返回找到的符合条件的第一个数据的位置（下标）
2.如果上游Observable始终没有出现满足判定条件的数据，find吐出undefined后完结，findIndex则吐出-1后完结
# 示例代码：
// find/findIndex
import 'rxjs/add/operator/find'
import 'rxjs/add/operator/findIndex'
import 'rxjs/add/operator/zip'

const source$ = Observable.of(3, 1, 4, 5, 6)
const isEven = x => x % 2 === 0
const find$ = source$.find(isEven)
const findIndex$ = source$.findIndex(isEven)
const zip$ = find$.zip(findIndex$)
zip$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# isEmpty 用于检查上游Observable对象是不是“空的”，所谓的“空的”Observable是指没有吐出任何数据就完结的Observable对象
# 特征：
1.isEmpty会在上游Observable吐出第一个数据时吐出false，然后完结
2.如果上游Observable直接吐出error，isEmpty并不会处理error，而是直接把error丢给下游，如果上游Observable处于既没有吐出数据，也没有完结的情况，isEmpty会一直等下去
3.只有到complete并且没有吐出任何数据的情况才会吐出true
# 示例代码：
import 'rxjs/add/operator/isEmpty'
import 'rxjs/add/observable/empty'
import 'rxjs/add/observable/never'
const empty$ = Observable.empty().isEmpty()
empty$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

const source$ = Observable.create(observer => {
    setTimeout(() => observer.complete(1), 1000)
})
source$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

const noempty$ = Observable.interval(1000).isEmpty()
noempty$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

```

#### 第七章	过滤数据流

```
# 过滤类操作符
# 过滤类操作符的基本功能是对上游Observable吐出每个数据进行判断，判断是否传递到下游，如果满足条件就可以传递到下游
# 判定函数，判断是是否能传递到下游
# 结果选择器，定制传递给下游的数据，示例：
function resultSelector(value,index){
	return [value,index]
}

# filter 过滤操作
# 示例代码：
import 'rxjs/add/operator/filter'
const filter$ = Observable.interval(1000).filter((x) => x % 2 === 0)
filter$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 注：filter并不支持“结果选择器”

# =============================

# first
# 特征：
1.可以没有判定函数，表示获取上游Observable吐出的第一个数据
2.判定函数，表示吐出符合判定函数的第一个数据
3.如果上游Observable到完结也没有吐出数据/符合条件的数据，那么first会向下游抛出error
4.第三个参数表示：如果没有符合条件的数据，则向下游返回定义的默认数据
# 示例代码：
import 'rxjs/add/operator/first'
const first$ = Observable.of(3, 1, 4, 2, 5).first()
first$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
const firstFilter$ = Observable.of(3, 1, 4, 2, 5).first(x => x % 2 === 0, (x, index) => [x, index])
firstFilter$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# last
# 特征：
1.向下游传递数据的时机，必须在上游Observable完结之后
2.同first一样，支持三个参数（判定函数，结果选择器，默认数据）
# 示例代码：
import 'rxjs/add/operator/last'
const lastFilter$ = Observable.of(3, 1, 4, 2, 5).last(x => x % 6 === 0, (x, index) => [x, index], 'haha')
lastFilter$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# take
# 特征：
1.“拿”，从上游Observable拿数据，拿够了就完结
2.只支持一个参数count，表示从上游Observable拿取的数据数量
# 示例代码：
import 'rxjs/add/operator/take'
import 'rxjs/add/operator/filter'
const take$ = Observable.of(3, 1, 4, 2, 5, 6).filter(x => x % 2 === 0).take(2)
take$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 注：如果count参数没有，默认不拿取任何数据，直接返回complete

# =============================

# takeLast
# 特征：
1.相当于一个可以获取多个数据last
2.同last一样，只有在上游数据完结的时候才能决定“最后”的数据是哪些，在吐出这些数据之后立即完结
3.takeLast只有确定上游数据完结的时候才能产生数据，而且是一次性产生所有数据，take是和上游Observable吐出数据时间上同步的
# 示例代码：
import 'rxjs/add/operator/takeLast'
import 'rxjs/add/operator/take'
const take$ = Observable.interval(1000).take(5).takeLast(3)
take$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# takeWhile
# 特征：
1.接收一个判定函数作为参数，这个判定函数有两个参数，分别代表上游数据和对应的序号，takeWhile会吐出数据，直到函数返回false
# 示例代码：
import 'rxjs/add/operator/takeWhile'
const takeWhile$ = Observable.interval(1000).takeWhile((x, index) => x < 10)
takeWhile$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# takeUntil
# 特征：
1.通过Observable对象来控制另一个Observable对象的数据产生
2.takeUntil一开始控制数据是开放的，上游Observable产生数据，那么就直接传递给下游，直到作为参数的Observable对象产生数据，就立即关闭，完结
# 示例代码：
import 'rxjs/add/operator/takeUntil'
const source$ = Observable.interval(1000)
const notifier$ = Observable.timer(3500)
const takeUntil$ = source$.takeUntil(notifier$)
takeUntil$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# skip
# 特征：
1.场景：跳过前N个之后全拿
2.接收一个count参数，会默认忽略上游Observable吐出的前count个数据，然后，从count+1个数据开始，就和上游Observable保持一致
3.如果上游吐出的数据不够count个，那skip产生的Observable就会在上游Observable完结的时候立即完结
# 示例代码：
import 'rxjs/add/operator/skip'
const skip$ = Observable.interval(1000).skip(3)
skip$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# skipWhile
# 特征：
1.跳过前面判断条件的数据，然后和上游Observable保持一致
# 示例代码：
import 'rxjs/add/operator/skipWhile'
const skipWhile$ = Observable.interval(1000).skipWhile(x => x < 3)
skipWhile$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# skipUntil
# 特征：
1.通过参数Observable对象来控制，直到参数notifier$吐出数据，然后停止跳过，和上游Observable保持一致
# 示例代码：
import 'rxjs/add/operator/skipUntil'
const source$ = Observable.interval(1000)
const notifier$ = Observable.timer(3500)
const skipUntil$ = source$.skipUntil(notifier$)
skipUntil$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# 回压控制
# 概念：在一个传输管道中，液体或者气体应该朝某一个方向流动，但是前方管道口径变小，这时候液体或者气体就会在管道中淤积，产生一个和流动方向相反的压力，因为这个压力的方向是往回走的，所以称为回压；Rxjs中，如果数据管道中某一个环节处理数据的速度跟不上数据涌入的速度，上游无法把数据推送给下游，就会在缓冲区中积压数据，这就相当于对上游施加了压力，这就是Rxjs世界中的“回压”
# 具体场景：zip合并两个上游Observable的时候，一个吐出速度快，一个慢，但是为了一一对应，就会造成吐出数据快的上游堆积了大量的数据
# 有损回压控制，既然处理不来，干脆舍弃一些数据

# throttleTime/debounceTime
# 都有“截流”，“去抖动”的含义；参数都是以毫秒为单位的时间
# throttleTime：限制在参数范围内，从上游传递给下游数据的个数；在指定的时间内，向下游传递数据，例如：上游每秒吐出数据，通过throttleTime做限制，实际每两秒才会向下游传递数据，中间的数据被舍弃了
# 示例代码：
import "rxjs/add/operator/throttleTime"
const throttleTime$ = Observable.interval(1000).throttleTime(2500)
throttleTime$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 说明：结果是0，3，6，9....，上游吐出0的时候，第一个数据自然不受限制，在0之后2.5秒内的数据全部舍弃，所以直到3的时候吐出数据，之后同样是2.5s内的数据都被舍弃，所以直到6才传递给下游，以此类推，造成了每3个数间隔吐出数据

# debounceTime：让传递给下游的数据间隔不能小于给定的参数时间；要等到上游在时间范围内不产生任何新的数据才把最新的数据吐给下游，如果在时间范围内产生了新的数据，就会重新开始计时
# 示例代码：
import 'rxjs/add/operator/debounceTime'
import 'rxjs/add/operator/filter'
const debounceTime$ = Observable.interval(1000).filter(x => x % 3 === 0).debounceTime(2000)
debounceTime$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 说明：0，3，6，9，12，15，18...通过filter产生的Observable对象每隔3秒会产生一个数据，设置debounceTime为2000ms，大于这个时间间隔，吐出上次最新的数据，过1s后，重新计算时间，再过2s之后吐出最新的能被3整除的数据
# 注：debounceTime在时间范围内上游吐出数据就会重新计时，throttleTime是舍弃在时间范围内的数据，如果时间范围已到还没有产生新的数据，会等到产生新的数据再进行计时

# =============================

# throttle/debounce
# 特征：之前的以Time为后缀结尾的操作符，参数只是时间，而throttle和debounce参数是一个函数，这个函数返回一个Observable对象
# 示例代码：
import 'rxjs/add/operator/throttle'
const durationSelector$ = (value) => Observable.timer(2000)
const throttle$ = Observable.interval(1000).throttle(durationSelector$)
throttle$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 结果：0，2，4，6...
# 说明：一开始的0不做限制，所以直接传递给下游，并在此之前以0为参数调用durationSelector$，返回Observable对象，在这个Observable对象吐出第一个数据之前，所有上游传递的数据都会被丢弃，当2000ms之后，Observable对象产生数据，打开闸门，吐出新的数据2，传递给下游，同时关上闸门，退订上次的Observable，从新以2为参数调用durationSelector产生新的Observable对象并订阅，直到再次传递新的数据，打开闸门；注：时机的控制通过返回的Observable对象吐出第一个数据的时机来进行控制

# debounce
# 特征：也是通过返回一个作为控制的Observable对象，通过这个Observable吐出的第一个数据作为控制
# 示例代码：

import 'rxjs/add/operator/debounce'
const durationSelector$ = (value) => Observable.timer(value % 3 === 0 ? 2000 : 1000)
const debounce$ = Observable.interval(1000).debounce(durationSelector$)
debounce$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 结果：1，2，4，5，7，8...
# 说明：第一个数据0，计算得到延迟时间2s，但是1s后上游就吐出了新的数据，时间被刷新，退订，并且把1作为参数计算出新的Observable，新的Observable延迟时间为1s，正好上游吐出2的时候，新的Observable吐出数据，返回最新的临近值1，然后把2作为参数带入，返回延迟1s的Observable，同样的可以向下游吐出临近的2，并且把3带入计算，得到延迟2s的Observable，但是1s后，吐出了新的数据，时间刷新，同时将4代入返回延迟1s的Observable，当上游吐出5的时候正好向下游吐出临近的最新值4，同时将5代入计算，算出新的Observable控制对象，之后以此类推，由于每个3都是间隔2s延迟，可是上游都是每秒返回，所以造成3的倍数总是被上游的下一秒打断，因此推送不到下游

# =============================

# audit/auditTime
# 特征：
1.类比throttle和throttleTime，throttle是将“节流时间”范围内，第一个数据传递给下游，audit是把最后一个数据传递给下游
# 示例代码：
import 'rxjs/add/operator/auditTime'
import 'rxjs/add/operator/filter'
const auditTime$ = Observable.interval(1000).filter(x => x % 3 === 0).auditTime(2000)
auditTime$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 结果0，3，6...

# 示例代码：
import 'rxjs/add/operator/auditTime'
import 'rxjs/add/operator/filter'
const auditTime$ = Observable.interval(1000).auditTime(2000)
auditTime$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 结果1，3，5...

# 示例代码：
# audit
import 'rxjs/add/operator/audit'
import 'rxjs/add/operator/mapTo'
import 'rxjs/add/operator/concat'
import 'rxjs/add/operator/take'
const source$ = Observable.interval(500).take(2).mapTo('A')
    .concat(Observable.interval(1000).take(3).mapTo('B'))
    .concat(Observable.interval(500).take(3).mapTo('C'))
const durationSelector = value => Observable.timer(800)
const audit$ = source$.audit(durationSelector);
audit$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# 示例代码：
# throttle
import 'rxjs/add/operator/throttle'
import 'rxjs/add/operator/mapTo'
import 'rxjs/add/operator/concat'
import 'rxjs/add/operator/take'
const source$ = Observable.interval(500).take(2).mapTo('A')
    .concat(Observable.interval(1000).take(3).mapTo('B'))
    .concat(Observable.interval(500).take(3).mapTo('C'))
const durationSelector = value => Observable.timer(800)
const throttle$ = source$.throttle(durationSelector);
throttle$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# 以上两个示例代码的结果见书P165

# =============================

# sampleTime
# 特征：
1.均匀的在时间段内取最后一个上游Observable传递的数据给下游
2.如果在时间段内上游没有吐出任何数据，那在这个时间块的结尾也不会传递给下游数据
# sample
# 特征：
1.传递的参数就是一个Observable对象
# 示例代码：
import 'rxjs/add/operator/sampleTime'
import 'rxjs/add/operator/mapTo'
import 'rxjs/add/operator/concat'
import 'rxjs/add/operator/take'
const source$ = Observable.interval(500).take(2).mapTo('A')
    .concat(Observable.interval(1000).take(3).mapTo('B'))
    .concat(Observable.interval(500).take(3).mapTo('C'))
const sampleTime$ = source$.sampleTime(800)
sampleTime$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# 根据数据序列作回压控制
# 不是以时间作为控制的标准，以数据内容作为控制的标准
# 例如：不希望处理重复的上游数据

# distinct
# 特征：
1.只返回从未出现过的数据
2.通过‘===‘来进行判断，对于对象这种复杂类型，提供了函数参数keySelector，用于比对什么样的属性
# 示例代码：
import 'rxjs/add/operator/distinct'
const distinct$ = Observable.of(
    { name: 'zhuzhu', age: 22 },
    { name: 'haha', age: 30 },
    { name: 'zhuzhu', age: 18 },
    { name: 'demo', age: 24 }
).distinct(x => x.name)
distinct$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)
# 注：可以想象操作符通过维护“唯一数据集合”的方式实现去重，这样就会造成问题，当数据量很大的时候，这个“唯一数据集合”也会很大，为了解决这个问题，可以通过第二个函数，来控制多长时间清空这个“唯一数据集合”，这个参数可以是一个Observable对象，每当这个Observable产生数据的时候，触发清空操作；具体是否使用需要考虑到不同的场景，合理使用

# =============================

# distinctUntilChanged
# 特征：
1.和distinct类似，但是不是和“唯一数据集合”进行比较，而是和上一个数据进行比较，保证不连续重复
2.提供可选参数，用于比较复杂数据类型，通过返回的bool进行判断
# 示例代码：
import 'rxjs/add/operator/distinctUntilChanged'
const distinctUntilChanged$ = Observable.of(
    { name: 'zhuzhu', age: 22 },
    { name: 'haha', age: 30 },
    { name: 'zhuzhu', age: 18 },
    { name: 'demo', age: 24 }
).distinctUntilChanged((a, b) => a.name === b.name)
distinctUntilChanged$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# distinctUntilKeyChanged
# 特征：distinctUntilChanged的简化版本，直接提供查重字段，实现去重
# 示例代码：
import 'rxjs/add/operator/distinctUntilKeyChanged'
const distinctUntilKeyChanged$ = Observable.of(
    { name: 'zhuzhu', age: 22 },
    { name: 'haha', age: 30 },
    { name: 'zhuzhu', age: 18 },
    { name: 'demo', age: 24 }
).distinctUntilKeyChanged('name')
distinctUntilKeyChanged$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# ignoreElements
# 特征：忽略掉所有的元素，只关心complete和error
# 示例代码：
import 'rxjs/add/operator/ignoreElements'
import 'rxjs/add/operator/take'
const ignoreElements$ = Observable.interval(1000).take(5).ignoreElements()
ignoreElements$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# elementAt
# 特征：
1.把上游数据当数组，获取指定下标的那一个数据
2.第二个参数可以指定没有对应下标数据时的默认值
3.如果不使用第二个参数，但同时也没有对应下标的值，那么会传递给下游error
# 示例代码：
import 'rxjs/add/operator/elementAt'
import 'rxjs/add/operator/take'
const elementAt$ = Observable.interval(1000).take(3).elementAt(3, 5)
elementAt$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

# =============================

# single
# 特征：用来检查上游是否只有一个满足对应条件的数据，如果为true，就向下游传递这个数据，如果为false，就向下游传递异常
# 示例代码：
import 'rxjs/add/operator/single'
import 'rxjs/add/operator/take'
const single$ = Observable.interval(1000).take(2).single(x => x % 2 === 0)
single$.subscribe(
    (value) => console.log(value),
    (error) => console.error(error),
    () => console.log('complete')
)

```

#### 第八章 转化数据流

```
# 转化类操作符
# 1.对每个数据作转化，上游的数据和下游的数据依然是一对一的关系
# 2.不转化单个数据，而是把数据重新组合

# map
# 特征：类似于数组中的map函数，对每个上游推来的数据进行转化
# 示例代码：
1.单个参数
import { Observable } from 'rxjs/Observable'
import 'rxjs/add/observable/of'
import 'rxjs/add/operator/map'

const source$ = Observable.of(3, 5, 2)
const mapFunc = (value) => value * 2
const result$ = source$.map(mapFunc)
result$.subscribe(console.log, null, () => console.log('complete'))

2.两个参数
const source$ = Observable(3, 5, 2)
const mapFunc = function (value, index) {
    return `${value}${this.separator}${index}`
}
const context = { separator: ':' }
const result$ = source$.map(mapFunc, context)
result$.subscribe(console.log, null, () => console.log('complete'))
# 注：不建议使用，按照函数式编程的原则，应该尽量让函数成为纯函数，函数的执行不应该依赖this

# 建议的使用方式
const source$ = Observable.of(3, 5, 2)
const context = { separator: ':' }
const mapFunc = (function (separator) {
    return (value, index) => `${value}${separator}${index}`
})(context.separator)
const result$ = source$.map(mapFunc)
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# mapTo
# 特征：转换为固定的值
# 示例代码：
import { Observable } from 'rxjs/Observable'
import 'rxjs/add/observable/of'
import 'rxjs/add/operator/mapTo'

const source$ = Observable.of(3, 4, 1)
const result$ = source$.mapTo(2)
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# pluck
# 特征：提取上游推过来的数据中的具体字段的值
# 示例代码
import { Observable } from 'rxjs/Observable'
import 'rxjs/add/observable/of'
import 'rxjs/add/operator/pluck'

const source$ = Observable.of({
    name: 'zhuzhu', age: 23
}, {
    name: 'hehe', age: 32
})
const result$ = source$.pluck('name')
result$.subscribe(console.log, null, () => console.log('complete'))

# 复杂结构
const source$ = Observable.of({
    name: 'zhuzhu', age: 23, job: { n: 'coder' }
}, {
    name: 'hehe', age: 32
})
const result$ = source$.pluck('job', 'n')
result$.subscribe(console.log, null, () => console.log('complete'))
# 结果：
coder
undefined
# 注：如果发现字段为空，对应会给下游传递undefined

# =============================

# 无损回压
# 无损回压控制就是把上游在一段时间内产生的数据放到一个数据集合里，然后把数据一次丢给下游，这里所说的数据集合可以是数组也可以是Observable对象，Rxjs提供两组操作符分别对应数组和Observable对象提供支持，支持数组的是以buffer开头的，支持Observable对象的是以window开头
# 在回压过程并没有对数据的内容进行处理，只是对数据的集合方式以及传递时机进行处理，这也能算是转化操作符的范畴
# 实际上就是将对数据的决策权交给了下游

# windowTime
# 特征：将时间范围内的数据放到Observable对象中，传递给下游
# 示例代码：
import 'rxjs/add/operator/windowTime'
const source$ = Observable.interval(100)
const result$ = source$.windowTime(400)
result$.subscribe((val$)=>{
    val$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))

result$订阅后收到的结果是Observable对象，需要对传到下游的数据订阅，获取数据；并且从第一个数据产生就开始
# 接受第二个参数，指定每个区块开始的时间间隔
Observable.interval(400,200) => 表示，400毫秒结束一个区块，并给下游传递数据，200表示，每200毫秒开始一个区块，并给下游传递数据，400表示的是结束，200表示的是开始，注意第二个和第一个参数的大小问题，可能会造成数据的丢失或者重叠
# 接受第三个参数，表示每个返回给下游的Observable中包含的数据个数，取前面的，指定数量之后，直接complete，

# =============================

# bufferTime
# 特征：将时间范围内的数据放到数组中传递给下游
# 示例代码：
import 'rxjs/add/operator/bufferTime'
const source$ = Observable.interval(100)
const result$ = source$.bufferTime(400)
result$.subscribe(console.log, null, () => console.log('complete'))
# result$订阅之后返回给下游的是数组，不会从开始就传递数据，第一个数据要等第一个时间区块完毕后，才会推给下游
# 第二和第三个参数和windowTime一样

# =============================

# windowCount
# 特征：根据数据数量定区间返回数据，返回Observable对象
# 示例代码：
import 'rxjs/add/operator/windowCount'
const source$ = Observable.interval(100)
const result$ = source$.windowCount(10)
result$.subscribe(val$ => {
    val$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))
# 第二个参数也是同样，表示开始间隔的数据个数，同样的注意和第一个参数之间的关系，可能会造成数据的丢失或者重叠

# =============================

# bufferCount
# 特征：根据数据数量定返回数据，返回数组的形式
# 示例代码：
import 'rxjs/add/operator/bufferCount'
const source$ = Observable.interval(100)
const result$ = source$.bufferCount(5, 3)
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# windowWhen
# 特征：根据Observable对象传递数据的时机，分块返回数据
# 示例代码：
import 'rxjs/add/operator/windowWhen'
const source$ = Observable.interval(100)
// 用于控制缓存窗口关闭，同时再次开启新的缓存窗口并重新调用函数
// 被调用的时机：被订阅的时刻或者缓冲区结束时调用
const closeSelector = () => Observable.timer(1000)
const result$ = source$.windowWhen(closeSelector)
result$.subscribe(val$ => {
    val$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))

# =============================

# bufferWhen
# 特征：根据Observable对象传递数据的时机，分块返回数据
# 示例代码：
import 'rxjs/add/operator/bufferWhen'
const source$ = Observable.interval(100)
const closeSelector = () => Observable.timer(1000)
const result$ = source$.bufferWhen(closeSelector)
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# windowToggle
# 特征：第一个参数Observable对象每产生一个数据，代表一个缓冲窗口开启，同时调用第二个参数closeSelecter，用来获得缓冲窗口结束
# closeSelecter函数是有参数的，参数就是第一个参数Observable对象产生的数据
# 示例代码：
import 'rxjs/add/operator/windowToggle'
const source$ = Observable.timer(0, 100)
const open$ = Observable.timer(0, 200)
const closeSelector = (value) => value % 2 === 0 ? Observable.timer(200) : Observable.timer(400)
const result$ = source$.windowToggle(open$, closeSelector)
result$.subscribe((val$) => {
    val$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))
# 注：通过弹珠图能够很好的理解

# ============================= 

# bufferToggle
# 特征：第一个参数Observable对象每产生一个数据，代表一个缓冲窗口开启，同时调用第二个参数closeSelecter，用来获得缓冲窗口结束
# closeSelecter函数是有参数的，参数就是第一个参数Observable对象产生的数据
# 示例代码：
import 'rxjs/add/operator/bufferToggle'
const source$ = Observable.timer(0, 100)
const open$ = Observable.timer(0, 200)
const closeSelector = (value) => value % 2 === 0 ? Observable.timer(200) : Observable.timer(400)
const result$ = source$.bufferToggle(open$, closeSelector)
result$.subscribe(console.log, null, () => console.log('complete'))
# 注：通过弹珠图能够很好的理解

# ============================= 

# window
# 特征：接受一个Observable对象作为控制器，每当产生数据，则既是前一个缓存期结束，也是下一个缓存期开始
# 示例代码：
import 'rxjs/add/operator/window'
const source$ = Observable.timer(0, 100)
const notice$ = Observable.timer(1000)
const result$ = source$.window(notice$)
result$.subscribe((val$) => {
    val$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))
# 注：结果中会包含3个complete，window完结的时候会输出complete，传给下游订阅的Observable会输出complete，由于notice$已完结，window产生的第二个Observable会立即complete，所以总共输出3个complete

# ============================= 

# buffer
# 特征：接受一个Observable对象作为控制器，每当产生数据，则既是前一个缓存期结束，也是下一个缓存期开始
# 示例代码：
import 'rxjs/add/operator/buffer'
const source$ = Observable.timer(0, 100)
const notice$ = Observable.timer(500, 500)
const result$ = source$.buffer(notice$)
result$.subscribe(console.log, null, () => console.log('complete'))

# 高阶的Map
# 所有高阶Map都有一个函数参数，这个函数参数和普通的map不同，不再是把上游传下来的数据转换成特定数据，而是把一个数据映射成一个Observable对象，类似如下：
const project = (value,index) => Observable.timer(200)
# 所谓的高阶map，不只是把返回的结果丢给下游就完事，而是把每个内部Observable中的数据作组合，通俗点说就是‘砸扁’，最终经过高阶map处理后的结果是一阶Observable对象

# ============================= 

# concatMap
# 特征：同concat，每个内部Observable对象中的数据，按顺序依次合并
# 示例代码：
import 'rxjs/add/operator/concatMap'
const project = (value, index) => Observable.interval(1000).take(value)
const source$ = Observable.interval(500)
const result$ = source$.concatMap(project)
result$.subscribe(console.log, null, () => console.log('complete'))
# 应用实例：鼠标拖动
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Mouse Move</title>
    <script src="https://cdn.bootcss.com/rxjs/5.5.8/Rx.js"></script>
    <style>
        #box {
            position: absolute;
            top: 0;
            left: 0;
            width: 100px;
            height: 100px;
            background-color: aquamarine;
        }
    </style>
</head>

<body>
    <div id="box"></div>
    <script>
        const box = document.getElementById('box')
        const mouseDown$ = Rx.Observable.fromEvent(box, 'mousedown')
        const mouseMove$ = Rx.Observable.fromEvent(box, 'mousemove')
        const mouseUp$ = Rx.Observable.fromEvent(box, 'mouseup')
        const mouseOut$ = Rx.Observable.fromEvent(box, 'mouseout')
        const drag$ = mouseDown$.concatMap((startEvent) => {
            const initialLeft = box.offsetLeft
            const initialTop = box.offsetTop
            const stop$ = mouseUp$.merge(mouseOut$)
            return mouseMove$.takeUntil(stop$).map(moveEvent => {
                return {
                    x: moveEvent.x - startEvent.x + initialLeft,
                    y: moveEvent.y - startEvent.y + initialTop
                }
            })
        })
        drag$.subscribe(event => {
            box.style.left = `${event.x}px`
            box.style.top = `${event.y}px`
        })
    </script>
</body>

</html>

# ============================= 

# mergeMap
# 特征：同merge，订阅每个内部Observable对象中的数据，有数据就推下游，先到先推
# 示例代码：
import 'rxjs/add/operator/mergeMap'
const project = (value, index) => Observable.interval(1000).take(value)
const source$ = Observable.interval(500)
const result$ = source$.mergeMap(project)
result$.subscribe(console.log, null, () => console.log('complete'))

# ============================= 

# switchMap
# 特征：总是订阅最新的内部Observable，退订之前的Observable，‘砸扁’之后的一阶Observable总是吐出最新的内部Observable中的最新数据
# 示例代码：
import 'rxjs/add/operator/switchMap'
const project = (value, index) => Observable.interval(1000).take(2)
const source$ = Observable.interval(500)
const result$ = source$.switchMap(project)
result$.subscribe(console.log, null, () => console.log('complete'))
# 结果：不会吐出任何数据，上游每500毫秒产生新的内部Observable对象，但是内部的Observable对象总是1s之后才会吐出数据，所以结果就是一直切换到最新的内部Observable，但是总没有数据吐到下游

# ============================= 

# exhaustMap
# 特征：和switchMap相反，先产生的内部Observable优先级总是更高，后产生的Observable能被使用的唯一机会就是前面的内部Observable完结，如果上游推送来了数据，但是之前的内部Observable还没有完结，则不会调用project方法产生新的内部Observable
# 示例代码：
import 'rxjs/add/operator/exhaustMap'
const project = (value, index) => Observable.interval(1000).take(value)
const source$ = Observable.interval(500)
const result$ = source$.exhaustMap(project)
result$.subscribe(console.log, null, () => console.log('complete'))
# 结果：当之前的内部Observable被消费了之后，新的内部Observable根据当时上游的推送的value来产生新的内部Observable

# ============================= 

# 高阶的MapTo
# concatMapTo,mergeMapTo,switchMapTo，参数不再是函数，而直接是一个Observable对象

# ============================= 

# expand
# 特征：类似于MergeMap，但是会将传递给下游的数据传递给自身，造成我先的循环
# 示例代码：
import 'rxjs/add/operator/expand'
const source$ = Observable.of(1)
const result$ = source$.expand((value, index) => Observable.of(value * 2).delay(100))
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# 数据分组
# 将上游传递的数据，根据特征（需要自行区分）放到不同的Observable对象中，将一个流拆分成多个流

# =============================

# groupBy
# 特征：又不同的key区分，分成多个流
# 场景：将取模0/1，分成两个流
# 示例代码：
import 'rxjs/add/operator/groupBy'
const source$ = Observable.interval(1000).take(10)
const result$ = source$.groupBy(x => x % 2)
// 订阅之后返回的是两个groupObservable实例，每个实例中都有key值用于区分
// result$.subscribe(console.log, null, () => console.log('complete'))
result$.subscribe((group$) => {
    group$.subscribe(console.log, null, () => console.log('complete'))
}, null, () => console.log('complete'))
# key是由groupBy中的函数参数的返回值决定的

# =============================

# partition
# 特征：将上游的数据通过过滤函数判断，返回true放到一个Observable中，返回false放到另一个Observable，将上游数据流分成两个流
# 示例代码：
import 'rxjs/add/operator/partition'
const source$ = Observable.interval(1000).take(10)
const [even$, odd$] = source$.partition(x => x % 2 === 0)
even$.subscribe(value => console.log(`even:${value}`))
odd$.subscribe(value => console.log(`odd:${value}`))
# 注：注意返回的结果

# =============================

# 累计数据
# 累计上游传向下游的数据

# scan
# 特征：类似reduce函数
# 示例代码：
import 'rxjs/add/operator/scan'
const source$ = Observable.interval(1000).take(10)
const result$ = source$.scan((prev, cur) => prev + cur)
result$.subscribe(console.log, null, () => console.log('complete'))

# =============================

# mergeScan
# 特征：类似scan，但是规约函数返回的Observable对象
# 每当上游传递数据，都会调用一次规约函数，并且订阅返回的Observable对象，之后这个Observable对象返回的任何数据都会推送给下游，mergeScan会记住传给下游的最后一个数据，作为prev数据
# 注：必须有seed参数
# 示例代码：
import 'rxjs/add/operator/mergeScan'
const source$ = Observable.interval(1000).take(10)
const result$ = source$.mergeScan((prev, cur) => Observable.of(prev + cur), 0)
result$.subscribe(console.log, null, () => console.log('complete'))
# 注：最好不要让规约函数返回的Observable对象包含多个数据，否则，下游接收到数据很容易分不清到底由哪个Observable对象产生的


```

#### 第九章 异常错误处理

```
# 异常处理不可避免
# 程序自身的代码在我们的控制中，程序的运行过程也可以把控，但是程序相关的外界环境因素不在我们的控制范围内，例如输入输出操作（文件写入写出），请求返回错误等，所以说程序之外的一切都不是百分百靠得住的，开发者的责任之一，就是要预料到这些异常可能发生，在代码中作出对应的预防措施

# try/catch 只支持同步运算
# 回调函数会造成回调地狱的出现，糟糕的编码方式
# Promise，优点：链式操作，最外层的catch可以捕捉到错误，缺点是状态无法更改，要么完成，要么失败，不能‘恢复’或者’重试‘

# RxJS的异常处理
# RxJS是函数式编程的事件，如果让一个函数可以抛出异常，那这个函数就不是纯函数，因为throw等于增加了一个新的函数出口，抛出去的异常会改变外部的状态

# 对错误异常的处理
1. 恢复（recover）
2. 重试（retry）

# 恢复：本来虽然产生了错误异常，但是依然让运行进行下去，比如：取数据的时候发生了错误，没有获得正确的数据，但是用一个默认值当作返回的结果，让运算继续

# 重试：当发生异常的时候认为这个异常只是临时的，重新尝试之前的运算，希望通过重试获得正确的结果

# 通过Observable对象进行异常处理
# 示例代码：
const source$ = Observable.interval(500).map(throwOnUnluckNumber)
source$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 结果：在4的时候抛出异常，然后程序结束
# 说明：在RxJS中，一个Observable对象只有一种终结状态，要么是完结，要么是出错，状态的改变只会进入其中一个，并且无法改变，所以在抛出异常之后，这个Observable对象终结，程序退出

# =============================

# catch
# 特征：通过catch操作符可以数据流中出现的异常，catch提供一个函数作为参数，函数的参数[err 上游抛出的错误；caught$ 代表上游紧邻的Observable对象，就是error$]，返回一个Observable对象; 当上游产生产生错误的时候，会调用函数，之后上游的数据终结进入错误状态，然后订阅返回的Observable对象，吐出的数据代替抛出的错误，相当于用其他的数据代替，往数据管道里塞另外的数据，让数据流得以继续
# 由于函数中存在caught$，如果函数的返回值是caught$对象，那么相当于‘重试’，如果再碰到问题则再次重试，进入循环
# 示例代码：
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const catch$ = error$.catch((err, caught$) => Observable.interval(1000))
catch$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))

# =============================

# retry
# 特征：重试，让上游的Observable重新走一遍，达到重试的目的；重试只对那些可能会正常的运算有意义，例如api请求出现错误是偶然的，再试一次很可能就会正常，对于那种语法上就错误的情景，无论重试多少次结果都是一样的，这样的重试没有任何意义
# 参数说明：接受一个数值作为参数，用于指定重试的次数，如果number为负数或者没有number参数，那么就是无限次重试
# 示例代码：
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retry(2)
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 结果：
0
1
2
3
0
1
2
3
0
1
2
3
Error: unlunky number
重试两次之后，将错误抛出，Observable终结，程序终止

# retry和catch配合
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retry(2).catch((err, caught$) => Observable.of(8))
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 结果：
0
1
2
3
0
1
2
3
0
1
2
3
8
complete
# 缺陷：立即重试往往结果还是一样，最好能够在重试的时间上做到控制

# =============================

# retryWhen
# 特征：接受一个函数作为参数，用于控制‘重试’的节奏和次数
# 参数说明：[err$ 代表的不是一个错误，而是由一组错误组成的Observable对象] 函数返回一个Observable对象，当上游传递一个错误的时候，就会调用这个函数，然后根据会的Observable对象吐出数据的时机，来控制何时'重试‘，当节奏控制器完结的时候，retryWhen返回的Observable对象也会立即完结
# 示例代码：
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retryWhen(err$ => Observable.interval(1000))
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 结果：
0
1
2
3
0
0
0
0
0
0
# 说明：每次返回的Observable吐出数据，就会‘重试’时间内一直吐出的数据都只能是0，retryWhen的参数函数只会执行一次

# 应用场景
1. 延时重试
# 示例代码：
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retryWhen(err$ => err$.delay(1000))
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 说明：每次重试到4的时候，err$就会多出一个错误，然后延迟1000ms之后吐出，然后再重试

2.用retryWhen实现retry
Observable.prototype.retryCount = function (maxCount) {
    return this.retryWhen((err$) => {
        return err$.scan((errorCount, err) => {
            if (errorCount >= maxCount) {
                throw err
            }
            return errorCount + 1
        }, 0)
    })
}

const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retryCount(2)
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))

3.延时并有上限的重试
Observable.prototype.retryWithDelay = function (maxCount, delayTime) {
    return this.retryWhen((err$) => {
    		// 关键在于控制返回的Observable对象吐出数据的时机
        return err$.scan((errorCount, err) => {
            if (errorCount >= maxCount) {
                throw err
            }
            return errorCount + 1
        }, 0).delay(delayTime)
    })
}

const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retryWithDelay(2, 1000)
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))

4.递增延时重试
Observable.prototype.retryWithExpotentialDelay = function (maxCount, initialDelay) {
    return this.retryWhen((err$) => {
    		// 关键在于控制返回的Observable对象吐出数据的时机
        return err$.scan((errorCount, err) => {
            if (errorCount >= maxCount) {
                throw err
            }
            return errorCount + 1
        }, 0).delayWhen((errorCount) => {
            const delayTime = Math.pow(2, errorCount - 1) * initialDelay
            return Observable.timer(delayTime)
        })
    })
}

const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const result$ = error$.retryWithExpotentialDelay(2, 500)
result$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))

# =============================

# finally
# 特征：同try/catch/finally中一样，无论上游数据完结或者出错，都会执行
# 示例代码：
const error$ = Observable.interval(500).map(throwOnUnluckNumber)
const catch$ = error$.catch((err, caught$) => Observable.of(8)).finally(() => console.log('final'))
catch$.subscribe(console.log, (err) => console.error(err), () => console.log('complete'))
# 结果：
0
1
2
3
8
complete
final

# 重试的本质：就是重新订阅，退订上一次的订阅，然后从新订阅上游Observable对象

```

#### 第十章 多播

```
# 多播：就是让一个数据流的内容被多个Observer订阅

# 数据流的多播，播放内容的三种方式
1.单播：一个播放者对应一个收听者，一对一的关系
2.广播：所有人都收听这个消息，听众不确定
3.多播：对订阅的收听者发送消息

# Cold Observable：每次被订阅都产生全新数据序列的数据流，数据的产生依赖是否被订阅，例如：interval，timer

# Hot Observable：产生Hot Observable对象的操作符数据源都是来自于外部，例如：Dom事件，Promise，或者外部的EventEmitter，数据的产生并不依赖是否被订阅，并且被订阅之后也只能获取之后新的数据，之前推出的数据无法得到，在没有订阅者的情况下，数据依然产生，只是没有传入到数据管道中

# Subject
# 函数式编程的世界中，有一个要求是保持“不可变性”，所以我们要把Cold Observable对象转换成一个Hot Observable对象，并不是去改变这个Cold Observable对象，而是产生一个新的Observable对象，包装之前的Cold Observable对象，这样在数据管道中，新的Observable对象就成为了下游，只要订阅这个下游Observable对象就能实现订阅Hot Observable对象
# 理解：通过中间的Observable对象，订阅一个外部的数据源，然后通过订阅这个中间Observable实现Hot，这就要求这个中间Observable对象既能订阅外部的数据流，同时也能被多个Observer订阅

# 虽然习惯把Observable对象的变量名带上$后缀，而且Subject也是一种Observable对象，但是Subject对象的变量名不需要加$

# 用Subject实现多播
# Hot Observable可以认为是“生产者”独立于订阅行为之外的Observable

# makeHot操作符，自定义一个makeHot操作符，实现Subject作为中间层对Cold Observable进行转化，转化为Hot Observable

# 示例代码：
import { Subject } from 'rxjs/Subject'

Observable.prototype.makeHot = function () {
    const cold$ = this
    const subject = new Subject()
    cold$.subscribe(subject)
    return subject
}

const hot$ = Observable.interval(1000).take(5).makeHot()

hot$.subscribe((value) => {
    console.log(`No1：${value}`)
}, null, () => console.log('complete'))

setTimeout(() => {
    hot$.subscribe((value) => {
        console.log(`No2：${value}`)
    }, null, () => console.log('complete'))
}, 1000)

# 缺陷：直接返回的subject对象，可以通过直接调用next，complete方法来影响下游

# 示例代码：
Observable.prototype.makeHot = function () {
    const cold$ = this
    const subject = new Subject()
    cold$.subscribe(subject)
    return Observable.create((observer) => subject.subscribe(observer))
}

const hot$ = Observable.interval(1000).take(5).makeHot()

hot$.subscribe((value) => {
    console.log(`No1：${value}`)
}, null, () => console.log('complete'))

setTimeout(() => {
    hot$.subscribe((value) => {
        console.log(`No2：${value}`)
    }, null, () => console.log('complete'))
}, 1000)

# Subject不能重复使用
# Subject也是一个Observable对象，一旦被调用了complete或者error函数，那么它作为Observable的生命周期也就结束了，之后再订阅将不再有消息，或者是一个由empty/empty throw操作符产生的直接完结/错误通知的Observable对象，Subject对象也可以通过unsubscribe取消订阅，之后再订阅会报错

# Subject可以有多个上游
# 虽然理论上subject可以合并多个数据流，但是，任何一个上游的Observable完结或者出错，都会造成Subject的终结，所以直接用Subject来合并数据流的工作是不合适的，想要合并数据流，最好是对上游的数据流先做合并处理，之后再用subject订阅上游数据流

# Subject的错误处理
# Subject可以把上游数据传递给下游的Observable，同样也可以把上游的错误传递给下游，需要注意的是，如果Subject的某个下游数据流产生错误异常，并且这个异常没有被Observer处理，那么这个Subject其他的Observer都会失败，这个Subject对象也被破坏，解决的办法：让下游的Observer都具备异常处理的能力，把错误信息自己处理掉，就不会造成Subject被破坏

# 支持多播的操作符





```

