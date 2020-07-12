## Promise手动封装

### 什么是 Promise？ Promise 怎么用？
Promise是ES6中管理异步操作的一种方法，是目前js异步编程的主流解决方案，遵循 Promise/A+ 规范实现  

###### 关于 Promise 的具体使用解释和使用方法可参考  
> MDN:  
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise  
廖雪峰的官方网站：   
https://www.liaoxuefeng.com/wiki/1022910821149312/1023024413276544  
    
###### Promise共有三种状态：  
> - pending：进行中  
> - onfulfulled：已成功  
> - rejected：已失败

###### Promise的状态变化只可能是两种：  
> - pending -> fulfilled  
> - pending -> rejected  

Promise一旦状态改变，就不能再改变，再次向Promise添加回调函数，会立即得到结果  

> Promise是一个构造函数，该构造函数接收一个函数（executor）作为参数，该函数接受两个函数作为参数:resolve，reject，分别用于将Promise变为onfulfilled和rejected，这两个函数都接收一个参数，此参数作为传递给then里边回调函数的参数

##### 原型方法：  
- then()：用于指定 Promise 成功，失败时的回调，该方法接受两个函数作为参数，第二个参数是可选的  
分别为 Promise 成功和失败时调用的回调函数  
这两个方法都接收一个参数，参数值为 resolve，reject 传递的值
- catch(): 用于指定 Promise 失败时的回调，该方法接收一个函数作为参数，为 Promise 失败时的回调，此函数接收一个参数，值为 reject 传递的值
- all()：用于多个 Promise 同时运行，该方法用于该方法接收一个数组作为参数，数组的每个项都为 Promise ，当所有 Promise 状态都为 onfulfilled 时，该 Promise 状态才会变为 onfulfilled , 然后将一个数组作为结果 ，数组中保存了每个 Promise 的运行结果，如果有一个 Promise 失败了，则立即改变该 Promise 状态为 rejected , 并且那个 rejected 的 Promise 失败的原因作为结果
- race()：就是多个 Promise 竞速 ，也接收一个数组作为参数，数组的每个项都为 Promise，与 all 不同的是， race 方法只要有一个 Promise 成功或者失败，则该 Promise 就成功或失败 ，并且将那个成功或失败 的Promise结果作为结果
- allSettled()：一样接收一个 Promise 数组，待所有 Promise 成功或者失败后，该 Promise 才会完成，以一个数组作为结果，存放每个 Promise 的结果 

###### 以上所有方法都会返回一个 Promise


```javascript

```