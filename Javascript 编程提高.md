## Javascript 编程提高

### 高阶函数

- 函数可以作为参数传递，称为高阶函数（简称HOC）
- 函数可以被其他函数返回。

- 定义unary函数，将一个接收多个参数的函数转换成只接收一个函数的参数

  ```
  // unary函数
  const unary = fn => fn.length === 1 ? fn : (arg) => fn(arg);
  
  const arrInt = [1,2,3].map(parseInt); // [1, NaN, NaN]
  // 通过unary函数中间过滤了一道，控制只传入一个参数
  const arrInt2 = [1,2,3].map(unary(parseInt)); // [1, 2, 3]
  
  # 知识点：
  [1,2,3].map(parseInt) ==> [1,2,3].map((item,index)=>parseInt(item,index))
  [1,NaN,NaN]
  
  function中的length
  length属性的作用：显示方法参数的期望参数个数
  function demo(item,index){}
  console.log(demo.length);
  ```

- memoized缓存函数

  ```
  // 纯函数的输出只依赖输入，所以可以对其做缓存操作
  // 阶乘函数只依赖输入，所以是纯函数
  const factorial = n => {
      if (n === 1) return 1;
      return n * factorial(n - 1);
  }
  console.time('one');
  memoFactorial(1000); // 1000*999*998...*1
  console.timeEnd('one'); // one: 0.115966796875ms
  ```

- zip函数，用于将两个数组合并成一个数组

  ```
  const zip = (arrLeft, arrRight, fn) => {
      let result = [];
      let index = 0;
      let maxLength = Math.max(arrLeft.length, arrRight.length);
      for (; index < maxLength; index++) {
          const res = fn(arrLeft[index], arrRight[index]);
          result.push(res);
      }
      return result;
  }
  zip([1,23,4], [2,4,5], (a, b) => a + b) // [3, 27, 9]
  ```

  

### 柯里化与偏应用

- 只接收一个参数的成为一元函数，接收两个参数称为两元参数，接收多个参数的称为多元函数。接收不确定参数的称为变参函数。

- 柯里化是把一个多参数函数转换成一个嵌套的一元函数的过程。

  ```
  // 定义柯里化函数
  const curry = (fn) => {
      return function curryFunc(...arg) {
          if (arg.length < fn.length) {
              return function () {
                  return curryFunc.apply(null, [...arg, ...arguments]);
              };
          }
          return fn.apply(null, arg);
      }
  };
  const func = (a, b) => console.log(a - b);
  curry(func)(1)(2) // -1
  ```

### 组合和管道

- Unix理念，一个程序只做好一件事情，如果要完成一项新的任务，重新构建要好于在旧程序中添加新属性。（理解为，单一原则，如果要完成新的任务，重新组合多个小的功能比改造原有的一个程序要好)

- 组合的概念，一个函数的输出作为另一个函数的输入，从右到左依次传递下去，该过程就是组合。

  ```
  // 定义组合函数
  // 简化写法
  const compose = (...fns) => (val) => fns.reverse().reduce((acc, fn) => fn(acc), val);
  // 完整写法
  const lihai = (...fns) => {return (val) => { console.log(val);return fns.reverse().reduce((acc, fn) => fn(acc), val)}};
  
  // reverse() 方法用于颠倒数组中元素的顺序。
   
  // 定义一系列小的函数
  const splitStr = str => str.split(' ');
  const getArrLen = arr => arr.length;
  // 组合并输出
  const getWords = lihai(getArrLen, splitStr);
  getWords('I am LengChui!') // 3
  ```

- 管道，和组合的功能一样，不过是从左到右的顺序。只是个人喜好问题。

  ```
  // 定义管道函数
  const pipe = (...fns) => val => fns.reduce((acc, fn) => fn(acc), val);
  // 可以达到和compose同样的输出
  const getWords2 = pipe(splitStr, getArrLen);
  getWords2('I am LengChui!')
  ```

- 管道和组合函数发生错误时的定位

  ```
  // 定义identity函数，将接收到的参数打印输出
  const identity = arg => {
      console.log(arg);
      return arg;
  }
  // 在需要的地方直接插入即可
  const getWords2 = pipe(splitStr, identity， getArrLen);
  ```

  



