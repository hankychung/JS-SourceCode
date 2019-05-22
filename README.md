<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [call/apply/bind](#callapplybind)
  - [call的实现](#call%E7%9A%84%E5%AE%9E%E7%8E%B0)
  - [apply的实现](#apply%E7%9A%84%E5%AE%9E%E7%8E%B0)
  - [bind的实现](#bind%E7%9A%84%E5%AE%9E%E7%8E%B0)
- [reduce](#reduce)
  - [description](#description)
  - [usage](#usage)
    - [run Promise In Sequence](#run-promise-in-sequence)
    - [pipe](#pipe)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## call/apply/bind

### call的实现
```
Function.prototype.myCall = function() {
    let obj = arguments[0],
    args = [...arguments].slice(1)
    if (obj._callFn != undefined) {
        console.error('reserved word for myCall has been TAKEN!')
        return
    }    
    obj._callFn = this
    obj._callFn(...args)
    obj._callFn = undefined    
}

function hello(ser, o) {
    console.log(`my name is %c${this.name}%c and age is %c${this.age}`, 'background:yellow', '', 'background:yellow')
    console.log(`secret words is %c${ser}%c and other words is %c${o}`, 'background:yellow', '', 'background:yellow')
}

let per = {
    name: 'Han',
    age: 18
}

hello.myCall(per, 'hello world', 'others')
```

### apply的实现
```
Function.prototype.myApply = function() {
    let obj = arguments[0],
    args = arguments[1]
    if (obj._applyFn != undefined) {
        console.error('reserved word for myApply has been TAKEN!')
        return
    }    
    obj._applyFn = this
    obj._applyFn(...args)
    obj._applyFn = undefined    
}

function hello(ser, o) {
    console.log(`my name is %c${this.name}%c and age is %c${this.age}`, 'background:yellow', '', 'background:yellow')
    console.log(`secret words is %c${ser}%c and other words is %c${o}`, 'background:yellow', '', 'background:yellow')
}

let per = {
    name: 'Han',
    age: 18
}

hello.myApply(per, ['hello world', 'others!'])
```

### bind的实现
```
Function.prototype.myBind = function() {
  let obj = arguments[0],
  args = [...arguments].slice(1)
  let bindFn = this 
  return function Fn() {
    console.dir(this)
    // 如果bind后的函数作为构造函数被调用，则不改变this的指向（还是指向实例对象），js中new对this指向的优先级大于bind
    if (this instanceof Fn) {
      return new bindFn(args.concat(...arguments))
    } else {
      bindFn.apply(obj, args.concat(...arguments))
    }    
  } 
}

function hello(ser, o, l) {
  this.tag = 'tag'
  console.dir(this)  
  console.log(`my name is %c${this.name}%c and age is %c${this.age}`, 'background:yellow', '', 'background:yellow')
  console.log(`secret words is %c${ser}%c and other words is %c${o}%c and last words is %c${l}`, 'background:yellow', '', 'background:yellow', '', 'background:yellow')
}

let per = {
  name: 'Han',
  age: 18
}

let myBind = hello.myBind(per, 'reserved')
myBind('other words!', 'other words')
myBind('hello words!', 'latest words!!')

let bindIns = new myBind()
```

> bind()的另一个最简单的用法（偏函数）是使一个函数拥有预设的初始参数。只要将这些参数（如果有的话）作为bind()的参数写在this后面。当绑定函数被调用时，这些参数会被插入到目标函数的参数列表的开始位置，传递给绑定函数的参数会跟在它们后面。

## reduce
### description
```
arr.reduce(callback[, initialValue])
```
> reduce 第一个参数: `callback` 是核心，它对数组的每一项进行“叠加加工”，其最后一次返回值将作为 reduce 方法的最终返回值。 它包含 4 个参数：
> 
> ① `previousValue`　表示“上一次” callback 函数的返回值
> 
> ② `currentValue`　数组遍历中正在处理的元素
> 
> ③ `currentIndex`　可选，表示 currentValue 在数组中对应的索引。如果提供了 initialValue，则起始索引号为 0，否则为 1
> 
> ④ `array`　可选，调用 reduce() 的数组
> 
> reduce 第二个参数: `initialValue` 可选，作为第一次调用 callback 时的第一个参数。如果没有提供 initialValue，那么数组中的第一个元素将作为 callback 的第一个参数。

### usage
#### run Promise In Sequence


#### pipe
> reduce 的另外一个典型应用可以参考函数式方法 pipe 的实现：pipe(f, g, h) 是一个 curry 化函数，它返回一个新的函数，这个新的函数将会完成 (...args) => h(g(f(...args))) 的调用。即 pipe 方法返回的函数会接收一个参数，这个参数传递给 pipe 方法第一个参数，以供其调用。

```
function f1(v) {
  console.log('running f1')
  return `f1 generate: ${v}`
}

function f2(v) {
  console.log('running f2')
  return `f2 generate: ${v}`
}

function f3(v) {
  console.log('running f3')
  return `f3 generate: ${v}`
}

// function pipe(...fns) {
//   return function (input) {
//     return fns.reduce((pre, cur) => {
//       return cur(pre)
//     }, input)
//   }
// }

const pipe = (...fns) => input => fns.reduce((pre, cur) => cur(pre), input)

console.log(pipe(f1, f2, f3)('hello world'))
```
