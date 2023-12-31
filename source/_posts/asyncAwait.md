---
title: async/await使用对比
top_img: '/CDN/wallpaper/cover_4.jpg'
cover: '/CDN/wallpaper/cover_4.jpg'
tags:
  - JavaScrip
categories:
  - 前端
abbrlink: 26266
---
整理一下async/await的优缺点，看看到底在哪些场合适合使用async/await。

## 优点1、它做到了真正的串行的同步写法，代码阅读相对容易

这个优点是没错，但是JavaScript的百分之九十的异步场合都是ajax，ajax就一定需要考虑异常，很有可能需要try...catch...来处理异常，所以优势并不明显。

## 优点2、对于条件语句和其他流程语句比较友好，可以直接写到判断条件里面

```js
  function a() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(222)
      }, 2222)
    })
  };

  async function f() {
    try {
      if ( await a() === 222) {
        console.log('yes, it is!') // 会打印
      }
    } catch (err) {
      // ...
    }
  }

  f();
```

如果`await a()`没有出错的可能性，还可以省掉try...catch...。

## 优点3、同样的，处理复杂流程时，在代码清晰度方面有优势

举个例子，有这样一套业务逻辑：有一个变量，类型是数组，如果它的length大于0，则遍历它进行下一步操作，如果length等于0，说明没有经历过ajax请求，则先ajax请求并赋值内容，然后再遍历它进行下一步操作；如果ajax的结果依旧是空，则显示toast，并中断流程。

- Promise写法（伪代码）：

```js
  let arr = [];

  new Promise((resolve) => {
    if (arr.length) {
      resolve();
    } else {
      ajax().then((res) => {
        if (res.data.length) {
          arr = res.data;
          resolve();
        } else {
          showToast('数据为空')
        }
      })
    }
  }).then(() => {
    arr.forEach(() => {});
  })
```

- async/await写法（伪代码）：

```js
  let arr = [];

  async function f() {
    if (!arr.length) {
      const res = await ajax();
      if (res.data.length) {
        arr = res.data;
      } else {
        showToast('数据为空')
        return;
      }
    }
    arr.forEach(() => {})
  }
  f();
```

可以看出：

- Promise写法，必须有if (arr.length) {resolve();}，而async/await写法不用考虑这个分支。
- Promise写法的代码不仅冗长，而且这还是在省略了一部分代码的前提下，showToast('数据为空')这个分支永远是pending状态，可能会带来一些问题。

## 无所谓优缺点1、无法处理promise返回的reject对象，要借助try...catch...

`await`无法处理reject对象，比如：

```js
  function g() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(222)
      }, 2222)
    })
  };

  async function f() {
    const y = await g();
    console.log(y);
  }

  f();
```

则`await g()`会直接报错，必须使用try...catch...捕获。

那么假定有3个ajax串行请求，Promise模式与async/await的对比如下：

- 1、允许统一处理reject的话：

```js
//Promise（伪代码）：
ajax1().then(() => {
  console.log('ajax1 success')
  return ajax2();
}).then(() => {
  console.log('ajax2 success')
  return ajax3();
}).then(() => {
  console.log('ajax3 success')
}).catch((err) => {
  console.log('可能打印ajax1或2或3的fail', err);
})
//async/await（伪代码）：
async function a() {
  try {
    await ajax1();
    console.log('ajax1 success')
    await ajax2();
    console.log('ajax2 success')
    await ajax3();
    console.log('ajax3 success')
  } catch (err) {
    console.log('可能打印ajax1或2或3的reject', err);
  }
}
a()
```

- 2、需要单独处理每一个ajax的reject的话：

```js
//Promise（伪代码）：
ajax1().then(() => {
  console.log('ajax1 success')
  return ajax2();
}, (err) => {
  console.log('ajax1的fail', err);
}).then(() => {
  console.log('ajax2 success')
  return ajax3();
}, (err) => {
  console.log('ajax2的fail', err);
}).then(() => {
  console.log('ajax3 success')
}, (err) => {
  console.log('ajax3的fail', err);
})
//async/await（伪代码）：
async function a() {
  try {
    await ajax1();
    console.log('ajax1 success')
  } catch (err) {
    console.log('打印ajax1的fail', err);
  }
  try {
    await ajax2();
    console.log('ajax2 success')
  } catch (err) {
    console.log('打印ajax2的fail', err);
  }
  try {
    await ajax3();
    console.log('ajax3 success')
  } catch (err) {
    console.log('打印ajax3的fail', err);
  }
}
a()
```

对比结果：从代码量上说，大同小异，就看你是否用的惯try...catch...。

为什么说用的惯，看这段代码，这段代码在上文贴过，想象一下，假如`if ( await a() === 222)` {的内容体有20行，会怎样——你会发现，`try`跟`catch`相距22行，很远，难以阅读，而且，内容体里面如果还有try...catch...怎么办？这就成了try...catch...的嵌套圣诞树，更难以阅读，最终解决办法只能是：如果if的内容体太长，尤其是try...catch...的嵌套圣诞树，就放弃`if ( await a() === 222)` {这种优雅的写法，改成`const res == await a();`这种写法，然后把这句单独做try...catch...。

```js
  function a() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(222)
      }, 2222)
    })
  };

  async function f() {
    try {
      if ( await a() === 222) {
        console.log('yes, it is!') // 会打印
      }
    } catch (err) {
      // ...
    }
  }

  f();
```

## 无所谓优缺点2、await只能串行，做不到并行

Promise可以轻松做到并行：

```js
ajax1();
ajax2();
//或者
Promise.all([ajax1(), ajax2()])
```

但是await做不到，它一定是阻塞的。await甚至可以阻塞for循环：

```js
  function g() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(222)
      }, 2222)
    })
  };

  async function f() {
    for (var i = 0; i < 10; i++) {
      const y = await g();
      console.log(y);
    }
  }

  f(); // 用时 22 秒才打印完
```

注意，await做不到并行，不代表async不能并行。只要await不在同一个async函数里就可以并行。比如：

```js
  function g() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(222)
      }, 2222)
    })
  };

  function f() {
    [1,1,1,1,1,1,1,1,1,1].forEach(async (v) => {
      const y = await g();
      console.log(y);
    })
  }

  f();
```

## 无所谓优缺点3、全局捕获错误必须用window.onerror，不像Promise可以专用window.addEventListener('unhandledrejection', function)，而window.onerror会捕获各种稀奇古怪的错误，造成系统浪费

尽管window.onerror的开销大，但是一个成熟的系统是一定要利用window.onerror做错误监控系统，所以，无所谓了。

## 缺点1、try...catch...内部的变量无法传递给下一个try...catch...

Promise和then/catch内部定义的变量，能通过then链条的参数传递到下一个then/catch，但是async/await的try内部的变量，如果用`let`和`const`定义则无法传递到下一个try...catch...，只能在外层作用域先定义好。

## 缺点2、async/await无法简单实现Promise的各种原生方法，比如.race()之类

如果真的编写一些工具库，确实可以实现Promise的各种原生方法，但放着Promise原生方法不用，却要写工具库，完全没必要。

## 什么场合用async/await，什么场合用Promise？

- 需要用到Promise各种便捷的方法（比如.race()之类）的时候，一定用Promise。
- 并行的请求最好用Promise。
- 不需要并行的场合，如果要传递参数，最好用Promise。
- 其他ajax场合，看你喜好try...catch...还是.catch()，以决定使用哪一方。
- 你必须看看下方对于拦截器的讨论。

## 额外讨论：ajax异常全部用拦截器处理，是否可以避免使用try...catch...？

拦截器对于后端业务代码出错，例如500错误，应当怎么处理呢？

### 如果：拦截器把200和500都归类到resolve

- 优点：共有的好处是只需要考虑200状态，所以确实不需要写try...catch...，也不需要.catch()。
- 缺点：对两个方案都有缺点，500归为resolve的话，语义比较拧巴，而且业务代码里永远需要有这种代码：

```js
if (res.code === 200) {

} else if (res.code === 500) {

}
```

### 如果：拦截器只将500错误归为reject，而200依然属于resolve

- 优点：共有的好处是不用一遍遍的写`if (res.code === XXX)`，因为try里面是200的处理代码，catch里面是500的处理代码，天然就分开了。而且，500错误归为reject，从语义上说不拧巴。
- 缺点：对两个方案都有缺点，必须用try...catch...或.catch()捕获reject，不能省略。

结论
1、200和500全归到resolve的前提下，真的可以避免try...catch...，但是又带来了`if (res.code === XXX)`。

2、如果喜欢写`if (res.code === XXX)`，就让拦截器把200和500都归类到resolve，如果不喜欢写`if (res.code === XXX)`，就500归类到reject。
