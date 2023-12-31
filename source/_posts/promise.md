---
title: Promise实现原理
top_img: '/CDN/wallpaper/16.jpg'
cover: '/CDN/wallpaper/16.jpg'
highlight_shrink: true
tags:
  - JavaScrip
categories:
  - 前端
abbrlink: 61384
---
在传统的异步编程中，如果异步之间存在依赖关系，我们就需要通过层层嵌套回调来满足这种依赖，如果嵌套层数过多，可读性和可维护性都变得很差，产生所谓“回调地狱”，而 Promise 将回调嵌套改为链式调用，增加可读性和可维护性。

## Promise 的调用流程

- `Promise`的构造方法接收一个 executor()，在`new Promise()`时就立刻执行这个 executor 回调
- `executor()`内部的异步任务被放入宏/微任务队列，等待执行
- `then()`被执行，收集成功/失败回调，放入成功/失败队列
- `executor()`的异步任务被执行，触发`resolve/reject`，从成功/失败队列中取出回调依次执行

## Promise A+规范

由于 ES6 的 Promise 实现需要遵循 Promise/A+规范，是规范对 Promise 的状态控制做了要求。Promise/A+的规范比较长，这里只总结两条核心规则：

- `Promise` 本质是一个状态机，且状态只能为以下三种：`Pending（等待态）`、`Fulfilled（执行态）`、`Rejected（拒绝态）`，状态的变更是单向的，只能从 Pending -> Fulfilled 或 Pending -> Rejected，状态变更不可逆
- `then`方法接收两个可选参数，分别对应状态改变时触发的回调。then 方法返回一个 promise。then 方法可以被同一个 promise 调用多次。

## then 的链式调用

先举个例子看一下 then 是如何链式调用的：

```js
const p1 = new Promise((resolve, reject) => {
  resolve(1);
});

p1.then((res) => {
  console.log(res);
  //then回调中可以return一个Promise
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(2);
    }, 1000);
  });
})
  .then((res) => {
    console.log(res);
    //then回调中也可以return一个值
    return 3;
  })
  .then((res) => {
    console.log(res);
  });
```

输出：1 2 3

### 如何实现链式调用

- 显然`then()`需要返回一个 Promise，这样才能找到 then 方法，所以我们会把 then 方法的返回值包装成 Promise。
- `then()`的回调需要拿到上一个`then()`的返回值
- `then()`的回调需要顺序执行，以上面这段代码为例，虽然中间 return 了一个 Promise，但执行顺序仍要保证是 1->2->3。我们要等待当前 Promise 状态变更后，再执行下一个 then 收集的回调，这就要求我们对 then 的返回值分类讨论

## 值穿透&状态已变更情况

- 值穿透：根据规范，如果 then() 接收的参数不是 function，那么我们应该忽略它。如果没有忽略，当 then()回调不为 function 时将会抛出异常，导致链式调用中断
- 处理状态为 resolve/reject 的情况：其实我们上边 then() 的写法是对应状态为`padding`的情况，但是有些时候，resolve/reject 在 then() 之前就被执行（比如`Promise.resolve().then()`），如果这个时候还把 then()回调 push 进 resolve/reject 的执行队列里，那么回调将不会被执行，因此对于状态已经变为`fulfilled`或`rejected`的情况，我们直接执行 then 回调

## 兼容同步任务

完成了 then 的链式调用以后，我们再处理一个前边的细节，然后放出完整代码。上文我们说过，Promise 的执行顺序是 `new Promise -> then()收集回调 -> resolve/reject 执行回调`，这一顺序是建立在 executor 是异步任务的前提上的，如果 executor 是一个同步任务，那么顺序就会变成 `new Promise -> resolve/reject 执行回调 -> then()收集回调`，resolve 的执行跑到 then 之前去了，为了兼容这种情况，我们给 resolve/reject 执行回调的操作包一个 setTimeout，让它异步执行。

## 结合之前的五步，实现了 Promise 的主要功能

```js
//Promise/A+规定的三种状态
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
  // 构造方法接收一个回调
  constructor(executor) {
    this._status = PENDING; // Promise状态
    this._value = undefined; // 储存then回调return的值
    this._resolveQueue = []; // 成功队列, resolve时触发
    this._rejectQueue = []; // 失败队列, reject时触发

    // 由于resolve/reject是在executor内部被调用, 因此需要使用箭头函数固定this指向, 否则找不到this._resolveQueue
    let _resolve = (val) => {
      //把resolve执行回调的操作封装成一个函数,放进setTimeout里,以兼容executor是同步代码的情况
      const run = () => {
        if (this._status !== PENDING) return; // 对应规范中的"状态只能由pending到fulfilled或rejected"
        this._status = FULFILLED; // 变更状态
        this._value = val; // 储存当前value

        // 这里之所以使用一个队列来储存回调,是为了实现规范要求的 "then 方法可以被同一个 promise 调用多次"
        // 如果使用一个变量而非队列来储存回调,那么即使多次p1.then()也只会执行一次回调
        while (this._resolveQueue.length) {
          const callback = this._resolveQueue.shift();
          callback(val);
        }
      };
      setTimeout(run);
    };
    // 实现同resolve
    let _reject = (val) => {
      const run = () => {
        if (this._status !== PENDING) return; // 对应规范中的"状态只能由pending到fulfilled或rejected"
        this._status = REJECTED; // 变更状态
        this._value = val; // 储存当前value
        while (this._rejectQueue.length) {
          const callback = this._rejectQueue.shift();
          callback(val);
        }
      };
      setTimeout(run);
    };
    // new Promise()时立即执行executor,并传入resolve和reject
    executor(_resolve, _reject);
  }

  // then方法,接收一个成功的回调和一个失败的回调
  then(resolveFn, rejectFn) {
    // 根据规范，如果then的参数不是function，则我们需要忽略它, 让链式调用继续往下执行
    typeof resolveFn !== 'function' ? (resolveFn = (value) => value) : null;
    typeof rejectFn !== 'function'
      ? (rejectFn = (reason) => {
          throw new Error(reason instanceof Error ? reason.message : reason);
        })
      : null;

    // return一个新的promise
    return new MyPromise((resolve, reject) => {
      // 把resolveFn重新包装一下,再push进resolve执行队列,这是为了能够获取回调的返回值进行分类讨论
      const fulfilledFn = (value) => {
        try {
          // 执行第一个(当前的)Promise的成功回调,并获取返回值
          let x = resolveFn(value);
          // 分类讨论返回值,如果是Promise,那么等待Promise状态变更,否则直接resolve
          x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
        } catch (error) {
          reject(error);
        }
      };

      // reject同理
      const rejectedFn = (error) => {
        try {
          let x = rejectFn(error);
          x instanceof MyPromise ? x.then(resolve, reject) : resolve(x);
        } catch (error) {
          reject(error);
        }
      };

      switch (this._status) {
        // 当状态为pending时,把then回调push进resolve/reject执行队列,等待执行
        case PENDING:
          this._resolveQueue.push(fulfilledFn);
          this._rejectQueue.push(rejectedFn);
          break;
        // 当状态已经变为resolve/reject时,直接执行then回调
        case FULFILLED:
          fulfilledFn(this._value); // this._value是上一个then回调return的值
          break;
        case REJECTED:
          rejectedFn(this._value);
          break;
      }
    });
  }
}
```

## 添加几个 Promise 的静态/实例方法

```js
//catch方法其实就是执行一下then的第二个回调
  catch(rejectFn) {
    return this.then(undefined, rejectFn)
  }

  //finally方法
  finally(callback) {
    return this.then(
      value => MyPromise.resolve(callback()).then(() => value),             //执行回调,并returnvalue传递给后面的then
      reason => MyPromise.resolve(callback()).then(() => { throw reason })  //reject同理
    )
  }

  //静态的resolve方法
  static resolve(value) {
    if(value instanceof MyPromise) return value //根据规范, 如果参数是Promise实例, 直接return这个实例
    return new MyPromise(resolve => resolve(value))
  }

  //静态的reject方法
  static reject(reason) {
    return new MyPromise((resolve, reject) => reject(reason))
  }

  //静态的all方法
  static all(promiseArr) {
    let index = 0
    let result = []
    return new MyPromise((resolve, reject) => {
      promiseArr.forEach((p, i) => {
        //Promise.resolve(p)用于处理传入值不为Promise的情况
        MyPromise.resolve(p).then(
          val => {
            index++
            result[i] = val
            if(index === promiseArr.length) {
              resolve(result)
            }
          },
          err => {
            reject(err)
          }
        )
      })
    })
  }

  //静态的race方法
  static race(promiseArr) {
    return new MyPromise((resolve, reject) => {
      //同时执行Promise,如果有一个Promise的状态发生改变,就变更新MyPromise的状态
      for (let p of promiseArr) {
        MyPromise.resolve(p).then(  //Promise.resolve(p)用于处理传入值不为Promise的情况
          value => {
            resolve(value)        //注意这个resolve是上边new MyPromise的
          },
          err => {
            reject(err)
          }
        )
      }
    })
  }
```
