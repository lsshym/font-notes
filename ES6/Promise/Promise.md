### 如果没有Promise

```javascript
// 用setTimeout模拟
let ajax = function (callback) {
    console.log('执行')
    // 请求成功后
    setTimeout(() => {
        callback && callback.call(this, 'test')
    }, 1000)
}

ajax(function (reqs) {
    console.log('请求1')
    console.log(reqs)
})
// 执行
// 请求1
// test
```

一个请求，没什么毛病，还行

```javascript
let ajax = function (callback) {
    console.log('执行')
    // 请求成功后
    setTimeout(() => {
        callback && callback.call(this, 'test')
    }, 1000)
}

ajax(function (reqs) {
    console.log('请求1')
    console.log(reqs)
    ajax(function (reqs) {
        console.log('请求2')
        console.log(reqs)
	})
})
// 回调嵌套的问题，Promise算是一个写法格式的优化
```

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
// Promise执行顺序的问题，新建的Promise会立即执行
```

resole的参数除了几个基本类型和引用类型外，还可以是一个Promise

```javascript
const p1 = new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('fail')), 3000)
})
const p2 = new Promise(function (resolve, reject) {
    setTimeout(() => resolve(p1), 1000)
})
p2
    .then(result => console.log(result))
    .catch(error => console.log(error))
```

### Promise.property.then

```javascript
// 这方法比较有意思
const getJSON = function (url) {
    const promise = new Promise(function (resolve, reject) {
        resolve()
    });
    return promise;
};
getJSON("/posts.json").then(function () {
    return '111';
}).then(function (post) {
    console.log(post)
    // then方法return的参数会作为下一个then的参数
});
// then返回的是一个Promise，这里还是没想太明白
const getJSON = function (url) {
    const promise = new Promise(function (resolve, reject) {
        resolve()
    });
    return promise;
};
getJSON("/posts.json").then(function () {
    return new Promise(function(resolve) {
        resolve('第一步结果')
    });
}).then(function (post) {
    console.log(post)
    return '666'
}).then(function(value) {
    console.log(value)
});
```

```javascript
function promise() {
    return new Promise((reslove,reject)=>{
        if(true) {
            return reslove(123)
        }
    })
}
let t1 = promise().then((va)=>{
    console.log(va)
    // 这里的return 居然是返回值，为什么是返回值，如果是返回值，很多问题迎刃而解
    return 1
    // return 1 就相当于resolve(1),既然如此要调用reject呢？
}).then((value)=>{
    console.log(value)
})
console.log(t1)
```

```javascript
// 这么写我就懂了，return 一个Promise实例
getJSON().then(() => {
    // 返回一个Promise实例，新的
    return new Promise((resolve, reject) => {
        resolve(1)
    })
}).then((value) => {
    console.log(value)
})
```

Promise的return没用，但是then的return有用

# then中的return

Promise连续请求的研究和试验

虽然then中的return 返回的值会当作下一个then的参数，但并没有什么卵用，同步的

```javascript
function promise() {
  return new Promise((reslove, reject) => {
    setTimeout(() => {
      reslove('第一次请求返回结果')
    },
    1000);
  });
};
let test = promise().then(function(value) {
  console.log(value);
  // 接下来发送第二次请求
  // 根据第一次请求返回结果去请求第二次
  // 好了现在模拟ajax发送请求返回数据，要把数据返回给下一个then
  let buf = ''
  setTimeout(() => {
    buf = ' 第二次'
  },
  1000);
  //
  return value + buf;

}).then((value) => {
  console.log(value)
})
```

![image-20200321131001265](C:\Users\Mr.Giraffe\AppData\Roaming\Typora\typora-user-images\image-20200321131001265.png)





```javascript
function promise() {
  return new Promise((reslove, reject) => {
    setTimeout(() => {
      // reslove的值作为参数返回
      reslove('第一次请求返回结果')
    },
    1000);
  });
};
// then返回一个promise
promise().then(function(value) {
  console.log(value);
  return new Promise((reslove, reject) => {
    setTimeout(() => {
      reslove('第二次请求')
    },
    1000);
  })
}).then(function(value) {
  // 也就是这个Promise
  console.log('111');
  console.log(value);
  console.log('222');
})

// 这里猜测可能和new类似，如果then的方法返回的是一个常量值，那就默认返回一个新建的Promise，这个常量作为then方法中的参数
// 如果return 返回的是一个手动创建的Promise，那就替换，由下边的then方法继续
// then方法中的return
```

