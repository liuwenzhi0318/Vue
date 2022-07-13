# Node.js
> 测试代码在gitee上
> 地址：https://gitee.com/gaohan888/node-js-learning/tree/master/node
## fs 文件系统模块

fs 模块是 Node.js 官方提供的、用来操作文件的模块。它提供了一系列的方法和属性，用来满足用户对文件的操作需求。 例如：

* fs.readFile() 方法，用来读取指定文件中的内容
* fs.writeFile() 方法，用来向指定的文件中写入内容



如果要在 JavaScript 代码中，使用 fs 模块来操作文件，则需要使用如下的方式先导入它

```js
const fs = require('fs')
```

require是啥：
![在这里插入图片描述](https://img-blog.csdnimg.cn/6114625ef01a4bdc81afe0b349f80e55.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAaGFuZ2FvMjMz,size_20,color_FFFFFF,t_70,g_se,x_16)
## fs.readFile()

使用 fs.readFile() 方法，可以读取指定文件中的内容，语法格式如下：

```
fs.readFile(path[, options], callback)
```

参数解读： 

* 参数1：必选参数，字符串，表示文件的路径。 
* 参数2：可选参数，表示以什么编码格式来读取文件。 
* 参数3：必选参数，文件读取完成后，通过回调函数拿到读取的结果



**fs.readFile() 的示例代码**
以 utf8 的编码格式，读取指定文件的内容，并打印 err 和 dataStr 的值:

```js
const fs = require('fs')
fs.readFile('test.txt', 'utf8', function (err, dataStr) {
    console.log(err);
    console.log('---------');
    console.log(dataStr);
}) 
```

**判断文件是否读取成功**

可以判断 err 对象是否为 null，从而知晓文件读取的结果:

```js
fs.readFile('./test.txt', 'utf8', function (err, result) {
    if (err) {
        console.log('文件读取失败' + err.message);
    }
    console.log('文件读取成功，内容是：' + result);
})
```





### fs.writeFile()

使用 fs.writeFile() 方法，可以向指定的文件中写入内容，语法格式如下：

```js
fs.writeFile(file, data[, options], callback)
```

参数解读： 

* 参数1：必选参数，需要指定一个文件路径的字符串，表示文件的存放路径。 
* 参数2：必选参数，表示要写入的内容。 
* 参数3：可选参数，表示以什么格式写入文件内容，默认值是 utf8。 
* 参数4：必选参数，文件写入完成后的回调函数。



**fs.writeFile() 的示例代码**

向指定的文件路径中，写入文件内容:

```js
fs.writeFile('./test.txt', 'hello Node.js', function (err) {
    console.log(err);
})
```





**判断文件是否写入成功**

可以判断 err 对象是否为 null，从而知晓文件写入的结果:

```js
fs.writeFile('./test.txt', 'hello Node.js', function (err) {
    if (err) {
        console.log('文件写入失败');
    }
    console.log('文件写入成功');
})
```



### 练习-考试成绩整理

使用 fs 文件系统模块，将素材目录下成绩.txt文件中的考试数据，整理到成绩-ok.txt文件中。 整理前，成绩.txt文件中的数据格式如下：

**aaa=99 bbb=98 ccc=97 ddd=96**

整理完成之后，希望得到的成绩-ok.txt文件中的数据格式如下:

**aaa=99**
**bbb=98**
**ccc=97**
**ddd=96**



代码：

```js
const fs = require('fs')
let arr = [];
let str = '';
fs.readFile('成绩.txt', 'utf8', function (err, dataStr) {
    if (err) {
        console.log('文件读取失败');
    }
    console.log(dataStr);
    arr = dataStr.split(' ');
    arr.forEach( item => {
        str += item + '\r\n';
    })
    
    fs.writeFile('成绩-ok.txt', str, function (err) {
        if (err) {
            console.log(失败);
        }
        console.log('成功');
    })
})

```



### 路径问题

在使用 fs 模块操作文件时，如果提供的操作路径是以 ./ 或 ../ 开头的相对路径时，很容易出现路径动态拼接错误的问题。 

原因：代码在运行的时候，会以执行 node 命令时所处的目录，动态拼接出被操作文件的完整路径。 解决方案：在使用 fs 模块操作文件时，直接提供完整的路径，不要提供 ./ 或 ../ 开头的相对路径，从而防止路径动态拼接的问题。



**__dirname**能表示当前文件所处的目录

示例代码：

```js
// __dirname 表示当前文件所出的目录
fs.readFile(__dirname + '/test.txt', 'utf8', function (err, dataStr) {
    if (err) {
        console.log('读取文件失败');
    }
    console.log(dataStr);
})
```





## path路径模块

path 模块是 Node.js 官方提供的、用来处理路径的模块。它提供了一系列的方法和属性，用来满足用户对路径的处理 需求。

* path.join() 方法，用来将多个路径片段拼接成一个完整的路径字符串
* path.basename() 方法，用来从路径字符串中，将文件名解析出来



如果要在 JavaScript 代码中，使用 path 模块来处理路径，则需要使用如下的方式先导入它：

```js
const path = require('path')
```



### path.join()

使用 path.join() 方法，可以把多个路径片段拼接为完整的路径字符串，语法格式如下：

```js
path.join([...paths])
```

参数解读： 

* ...paths <string>  路径片段的序列 
* 返回值: <string>



**path.join()** 代码实例：

```js
const path = require('path')

const pathStr = path.join('/a', '/b/c', '../', './d', 'e')
console.log(pathStr);


const pathStr2 = path.join(__dirname, './1.txt')
console.log(pathStr2);
```



> 注意：今后凡是涉及到路径拼接的操作，都要使用 path.join() 方法进行处理。不要直接使用 + 进行字符串的拼接。



###  path.basename()

使用 path.basename() 方法，可以获取路径中的最后一部分，经常通过这个方法获取路径中的文件名，语法格式如下：

```js
path.basename(path[,ext])
```

参数解读： 

* path <string>  必选参数，表示一个路径的字符串 
* ext <string>  可选参数，表示文件扩展名 
* 返回: <string>  表示路径中的最后一部分



 **path.basename()** 的代码示例

```js
const fpath = 'a/b/c/index.html' // 文件的存放路径

var fullName = path.basename(fpath)
console.log(fullName); // 输出 index.html

var nameWithoutExt = path.basename(fpath, '.html')
console.log(nameWithoutExt); // 输出 index
```





### path.extname() 

使用 path.extname() 方法，可以获取路径中的扩展名部分，语法格式如下：

```js
path.extname(path)
```

* path 必选参数，表示一个路径的字符串 
* 返回:  返回得到的扩展名字符串



**path.extname()** 的代码示例

```js
const fpath = 'a/b/c/index.html' // 文件的存放路径

const fext = path.extname(fpath)
console.log(fext); // 输出 .html
```





## http模块

http 模块是 Node.js 官方提供的、用来创建 web 服务器的模块。通过 http 模块提供的 http.createServer() 方法，就 能方便的把一台普通的电脑，变成一台 Web 服务器，从而对外提供 Web 资源服务。



如果要希望使用 http 模块创建 Web 服务器，则需要先导入它：

```js
const http = require('http')
```



在 Node.js 中，我们不需要使用 IIS、Apache 等这些第三方 web 服务器软件。因为我们可以基于 Node.js 提供的 http 模块，通过几行简单的代码，就能轻松的手写一个服务器软件，从而对外提供 web 服务。



### 创建最基本的web服务器

① 导入 http 模块 

② 创建 web 服务器实例 

③ 为服务器实例绑定 request 事件，监听客户端的请求 

④ 启动服务器



示例代码:

```js
const http = require('http')
// 调用 http.createServer() 方法，即可快速创建一个 web 服务器实例：
const server = http.createServer()
// 为服务器实例绑定 request 事件，即可监听客户端发送过来的网络请求：
server.on('request', (req, res) => {
    // 只要又客户端来请求我们自己的服务器，就会触发request事件，从而调用这个事件处理函数
    console.log('someone visit our web server');
})
// 调用服务器实例的 .listen() 方法，即可启动当前的 web 服务器实例：
server.listen(8080, () => {
    console.log('http server running at http://127.0.0.1');
})
```



### req请求对象

只要服务器接收到了客户端的请求，就会调用通过 server.on() 为服务器绑定的 request 事件处理函数。

如果想在事件处理函数中，访问与客户端相关的数据或属性，可以使用如下的方式：

```js
server.on('request', (req, res) => {
    // 只要又客户端来请求我们自己的服务器，就会触发request事件，从而调用这个事件处理函数
    const str = `your request url is ${req.url}, and request method is ${req.method}`
    console.log('someone visit our web server');
    console.log(str);
})
```



### res响应对象

在服务器的 request 事件处理函数中，如果想访问与服务器相关的数据或属性，可以使用如下的方式：

```js
server.on('request', (req, res) => {
    // 只要又客户端来请求我们自己的服务器，就会触发request事件，从而调用这个事件处理函数
    const str = `your request url is ${req.url}, and request method is ${req.method}`
    console.log('someone visit our web server');
    console.log(str);

    // res.end()方法的作用
    // 向客户端发送指定内容，并结束这次请求的处理过程
    res.end(str);
})
```





### 解决中文乱码问题

当调用 res.end() 方法，向客户端发送中文内容的时候，会出现乱码问题，此时，需要手动设置内容的编码格式：

```js
server.on('request', (req, res) => {
    // 只要又客户端来请求我们自己的服务器，就会触发request事件，从而调用这个事件处理函数
    const str = `your request url is ${req.url}, and request method is ${req.method} 中文不乱码`
    console.log('someone visit our web server');
    console.log(str);
    // 为了防止中文显示乱码的问题，需要设置响应头content-type的值为 text/html;charset=utf-8
    res.setHeader('Content-Type', 'text/html; charset=utf-8')
    // res.end()方法的作用
    // 向客户端发送指定内容，并结束这次请求的处理过程
    res.end(str);
})
```



### 根据不同url响应不同的html内容

**核心实现步骤**

① 获取请求的 url 地址 

② 设置默认的响应内容为 404 Not found 

③ 判断用户请求的是否为 / 或 /index.html 首页 

④ 判断用户请求的是否为 /about.html 关于页面 

⑤ 设置 Content-Type 响应头，防止中文乱码 

⑥ 使用 res.end() 把内容响应给客户端



示例代码：

```js
server.on('request', (req, res) => {
  // 1. 获取请求的 url 地址
  const url = req.url
  // 2. 设置默认的响应内容为 404 Not found
  let content = '<h1>404 Not found!</h1>'
  // 3. 判断用户请求的是否为 / 或 /index.html 首页
  // 4. 判断用户请求的是否为 /about.html 关于页面
  if (url === '/' || url === '/index.html') {
    content = '<h1>首页</h1>'
  } else if (url === '/about.html') {
    content = '<h1>关于页面</h1>'
  }
  // 5. 设置 Content-Type 响应头，防止中文乱码
  res.setHeader('Content-Type', 'text/html; charset=utf-8')
  // 6. 使用 res.end() 把内容响应给客户端
  res.end(content)
})
```


