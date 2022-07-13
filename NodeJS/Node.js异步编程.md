# Node.js 异步编程
> 所以测试代码在gitee上
> https://gitee.com/gaohan888/node-js-learning/tree/master/node%E5%BC%82%E6%AD%A5%E7%BC%96%E7%A8%8B

## 概念

同步API：只有当前API执行完成后，才能继续执行下一个API

```js
for (let i = 0; i < 10000; i++) {
    console.log(i);
}
console.log('同步代码执行');
```

> 只有上面一万行数值打印完，才会打印'同步代码执行'

异步API：当前API的执行不会阻塞后续代码的执行

```js
console.log('before');
setTimeout(
   () => { console.log('last');
}, 2000);
console.log('after');
```

> setTimeout定时器要在2s秒后才执行，js引擎不会卡在定时器这，会先执行同步代码，等同步代码执行完再执行异步代码定时器(在这只需要先记住定时器是异步代码)





## 同步API, 异步API的区别（ 获取返回值 ）

同步API可以从返回值中拿到API执行的结果, 但是异步API是不可以的

**同步：**

```js
// 同步
function sum (n1, n2) { 
	return n1 + n2;
} 
const result = sum(10, 20); // result 值为 30
```

**异步：**

```js
function getMsg () { 
	setTimeout(function () { 
		return { msg: 'Hello Node.js' }
	}, 2000);
}
const msg = getMsg(); // msg 的值是 undefined
```

> 所以异步函数没法用返回值获取值









## 回调函数

自己定义函数让别人去调用。

使用回调函数可以获取异步API执行结果

```js
function getMsg (callback) {
    setTimeout(() => {
        let a = '异步函数结果'
        callback(a)
    }, 2000)
}

getMsg((result) => {
    console.log(result); // 异步函数结果
})
```







## 代码执行顺序分析

```js
console.log('代码开始执行');
setTimeout(() => {
	console.log('2秒后执行的代码');
}, 2000);
setTimeout(() => {
	console.log('0秒后执行的代码');
}, 0)
console.log('代码结束执行')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d47cf1c17b14613a4bb8daf40469766.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaGFuZ2FvMjMz,size_20,color_FFFFFF,t_70,g_se,x_16)


> 异步代码执行区的异步函数执行完成，将要执行专属的回调函数时，就会将回调函数放入回调函数队列，等同步代码执行区的代码执行完成后，就把回调函数队列的回调函数加入同步代码执行区。





## Node.js中的异步API

```js
fs.readFile('./demo.txt', (err, result) => {});
```

> 读文件函数在读取结束后，会将读到的结果以参数的形式传给回调函数，由回调函数来执行。

```js
var server = http.createServer();
server.on('request', (req, res) => {})
```

> 在客户端发送了请求后，将请求的内容以参数的形式传给回调函数的形参res



如果异步API后面代码的执行依赖当前异步API的执行结果，但实际上后续代码在执行的时候异步API还没有返回结果，这个问题要怎么解决呢？

需求：按顺序读取A文件，B文件，C文件

```js
const fs = require('fs')

fs.readFile('a.txt', (err, data) => {
    console.log('第一个执行', data);
    fs.readFile('b.txt', (err, data) => {
        console.log('第二个执行', data);
        fs.readFile('c.txt', (err, data) => {
            console.log(data);
        })
    })
})
```

> 连续嵌套着的回调函数可读性非常差，也称为回调地狱

## Promise

Promise出现的目的是解决Node.js异步编程中回调地狱的问题。

**promise小案例展示：**

```js
let promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        if (true) {
            resolve({name: 'aaaaa'})
        } else {
            reject('失败了')
        }
    }, 2000);
});

promise.then(result => {
    console.log(result);
}).catch( err => {
    console.log(err);
})
```



**Promise解决 按顺序读取A文件，B文件，C文件 的回调地狱问题**：

```js
function p1 () {
    return new Promise((resolve, reject) => {
        fs.readFile('a.txt', 'utf-8', (err, data) => {
            resolve(data)
        })
    })
}

function p2 () {
    return new Promise((resolve, reject) => {
        fs.readFile('b.txt', 'utf-8', (err, data) => {
            resolve(data)
        })
    })
}

function p3 () {
    return new Promise((resolve, reject) => {
        fs.readFile('c.txt', 'utf-8', (err, data) => {
            resolve(data)
        })
    })
}

p1().then(r1 => {
    console.log(r1);
    return p2()
}).then(r2 => {
    console.log(r2);
    return p3()
}).then(r3 => {
    console.log(r3);
})
```



## 异步函数

异步函数是异步编程语法的终极解决方案，它可以让我们将异步代码写成同步的形式，让代码不再有回调函数嵌套，使代码变得清晰明了。



**async 关键字**

1. 普通函数定义前加async关键字 普通函数变成异步函数

   ```js
   const fn = async () => {};
   async function fn() {}
   ```

2. 异步函数默认返回promise对象

   ```js
   async function fn() {}
   console.log(fn()); // Promise { undefined }
   ```

3. 在异步函数内部使用return关键字进行结果返回 结果会被包裹的promise对象中 return关键字代替了resolve方法

   ```js
   async function fn() {
       return 'abc'
   }
   console.log(fn()); // Promise { 'abc' }
   ```

4. 在异步函数内部使用throw关键字抛出程序异常

   ```js
   async function fn() {
       throw '发生了错误';
       return 'abc'
   }
   console.log(fn());
   ```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f6df5180fb824bfd92ed70dd4e4416b0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaGFuZ2FvMjMz,size_20,color_FFFFFF,t_70,g_se,x_16)


5. 调用异步函数再链式调用then方法获取异步函数执行结果

   ```js
   async function fn() {
       return 'abc'
   }
   // console.log(fn());
   fn().then(res => {
       console.log(res);
   })
   ```

6. 调用异步函数再链式调用catch方法获取异步函数执行的错误信息

   ```js
   async function fn() {
       throw '发生了错误';
       return 'abc'
   }
   // console.log(fn());
   fn().then(res => {
       console.log(res);
   }).catch(err => {
       console.log(err);
   })
   ```





**await关键字**

1. await关键字只能出现在异步函数中

2. await promise await后面只能写promise对象 写其他类型的API是不不可以的

3. await关键字可是暂停异步函数向下执行 直到promise返回结果



```js
async function fn1() {
    return 'fn1'
}

async function fn2() {
    return 'fn2'
}

async function fn3() {
    return 'fn3'
}


async function run () {
    let r1 = await fn1();
    let r2 = await fn2();
    let r3 = await fn3();
    console.log(r1);
    console.log(r2);
    console.log(r3);
}

run()
```



**用 async 和 await 解决 按顺序读取A文件，B文件，C文件 的回调地狱问题：**

```js
const fs = require('fs')
// fs.readFile() 这个方法不返回Promise对象
// promisify 可以对异步Api进行包装，让包装的方法返回Promise对象以支持 async await 语法
const promisify = require('util').promisify;
const readFile = promisify(fs.readFile);

async function run () {
    let r1 = await readFile('a.txt', 'utf-8')
    let r2 = await readFile('b.txt', 'utf-8')
    let r3 = await readFile('c.txt', 'utf-8')
    console.log(r1);
    console.log(r2);
    console.log(r3);
}
```


