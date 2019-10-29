# 手写一个promise
promise主要解决的是 回调地狱 问题，学习promise的时候，可以对比着回调函数，例如某些http请求如果用promise实现是怎样的，如果用回调函数写又是怎样的。接下来是一个手写的简单的promise,让我们一起揭开promise的本来面目！！

```javascript
const PENDING = Symbol('pending')
  const FULFILLED = Symbol('resolved')
  const REJECTED = Symbol('reject')
  function MyPromise(fn){
      if(typeof fn!=='function'){
          throw new Error('fn must be a function')
      }
      let state = PENDING
      let value = null
      let handler = {}
      function resolve(result){
          try{
              state=FULFILLED
              value=result
              handler.onFulfill(value)
          }catch(err){
              reject(err)
          }
      }
      function reject(error){
          state=REJECTED;
          value=error;
      }
      this.then=(onFulfill,onReject)=>{
          switch(state){
              case FULFILLED:onFulfill(value) 
                  break
              case REJECTED:onReject(value)
                  break
              case PENDING:handler = { onFulfill,onReject }
          }
      }
      fn(resolve,reject)
  }
  let p=new MyPromise((resolve,reject)=>{
      setTimeout(()=>{
          console.log(3,' setTimeout ')
          resolve('hello promise')
      },3000)
  })
  console.log(1,p)
  p.then((val)=>{
      console.log(4,val)
  })
  console.log(2,'哈哈')
```
## 运行结果
![运行结果](/img/promise001.png)

## 相关案例
之前看过一个面试题：
实现一个sleep函数：
执行
sleep(3000).then(()=>{
   console.log('....')
})
满足3s后执行console.log

很像promise，草草的写了个实现方式，希望各位指正或者有更好的实现方式提出
```javascript
let sleep=function(ms){
    let resolve=function(){
        console.log('呵呵呵')
    };
    let fn = function(){
        setTimeout(()=>{
            resolve()
        },ms)
        return fn
    } 
    Function.prototype.then=function(fun){
        resolve=fun;
    }
    return fn();
}
sleep(3000).then(()=>{console.log('哈哈哈')})
```
