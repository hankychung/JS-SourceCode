# JS-SourceCode

## 手敲call/apply/bind

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
