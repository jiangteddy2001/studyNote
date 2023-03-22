# Nodejs

参考资料：

https://blog.csdn.net/m0_52316372/article/details/124759435?spm=1001.2014.3001.5502

https://brucecai55520.gitee.io/bruceblog/notes/nodejs/node.html#%E5%88%9D%E8%AF%86-nodejs

## 1 初识 Nodejs

![image-20230310153202072](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101532116.png)

Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine

Node.js® 是一个基于 Chrome V8 引擎 的 JavaScript 运行时环境

基于 Express 框架 (opens new window)，可以快速构建 Web 应用
基于 Electron 框架 (opens new window)，可以构建跨平台的桌面应用
基于 restify 框架 (opens new window)，可以快速构建 API 接口项目
读写和操作数据库、创建实用的命令行工具辅助前端开发、etc…

![image-20230310152455511](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101525674.png)

总结

- V8 引擎负责解析和执行 JavaScript 代码
- 内置 API 是由运行环境提供的特殊接口，只能在所属的运行环境中被调用

## 2 fs文件系统模块

### 2.1 什么是 fs 文件系统模块

fs 模块是 Node.js 官方提供的、用来操作文件的模块。提供了一系列的方法和属性，用来满足对文件的操作需求
如果要在 JavaScript 代码中，使用 fs 模块来操作文件，则需要使用如下的方式先导入它

```
const fs = require("fs")
```



### 2.2 读取文件

`fs.readFile(path[, option], callback)`读取指定文件中的内容--------[]里面表示的可选参数
`path` 必选参数，字符串，文件路径
`option` 可选参数，设置字符集
`callback` 必选参数，文件读取完成后的回调函数

```js
// 1. 导入 fs 模块，来操作文件
const fs = require('fs')

// 2. 调用 fs.readFile() 方法读取文件
//    参数1：读取文件的存放路径
//    参数2：读取文件时候采用的编码格式，一般默认指定 utf8
//    参数3：回调函数，拿到读取失败和成功的结果  err  dataStr
fs.readFile('./files/1.txt', 'utf8', function(err, dataStr) {
  
  // 2.1 打印失败的结果
  // 如果读取成功，则 err 的值为 null
  // 如果读取失败，则 err 的值为 错误对象，dataStr 的值为 undefined
  console.log(err)
  console.log('-------')
  
  // 2.2 打印成功的结果
  console.log(dataStr)
})

//一般逻辑可以这么写：
fs.readFile('./files/11.txt', 'utf8', function(err, dataStr) {
  if (err) {
    return console.log('读取文件失败！' + err.message)
  }
  console.log('读取文件成功！' + dataStr)
})

```

测试方法：shift+鼠标右键  打开powershell

![image-20230312100133570](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121001609.png)

![image-20230312100018549](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121000602.png)

### 2.3 写入文件

fs.writeFile(path, data[, option], callback) 向指定的文件中写入内容
path 必选参数，字符串，文件路径
data 必选参数，写入的内容
option 可选参数，设置字符集，默认值是 utf8
callback 必选参数，文件写入完成后的回调函数
注意：写入会覆盖原内容

```js
const fs = require('fs')

// 2. 调用 fs.writeFile() 方法，写入文件的内容
//    参数1：表示文件的存放路径
//    参数2：表示要写入的内容
//    参数3：回调函数
fs.writeFile('./files/3.txt', 'ok123', function(err) {
  // 2.1 如果文件写入成功，则 err 的值等于 null
  // 2.2 如果文件写入失败，则 err 的值等于一个 错误对象
  // console.log(err)
//判断是否写入成功
  if (err) {
    return console.log('文件写入失败！' + err.message)
  }
  console.log('文件写入成功！')
})

```

![image-20230312100417571](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121004602.png)

### 2.4 流式文件写入

```js
// 同步、异步、简单文件的写入都不适合大文件的写入，性能较差，容易导致内存溢出
var fs = require('fs')

// 创建一个可写流
var ws = fs.createWriteStream('hello3.txt')

ws.once('open', function () {
  console.log('流打开了~~')
})

ws.once('close', function () {
  console.log('流关闭了~~')
})

// 通过ws向文件中输出内容
ws.write('通过可写流写入文件的内容')
ws.write('1')
ws.write('2')
ws.write('3')
ws.write('4')

// 关闭流
ws.end()
```

### 2.5 路径动态拼接问题 `__dirname`

- 在使用 fs 模块操作文件时，如果提供的操作路径是以 `./` 或 `../` 开头的相对路径时，容易出现路径动态拼接错误的问题
- 原因：代码在运行的时候，会以执行 node 命令时所处的目录，动态拼接出被操作文件的完整路径
- 解决方案：在使用 fs 模块操作文件时，直接提供完整的路径，从而防止路径动态拼接的问题
- `__dirname` 获取文件所处的绝对路径

```
fs.readFile(__dirname + '/files/1.txt', 'utf8', function(err, data) {
  ...
})
```

### 2.6 其他操作

验证路径是否存在：

- `fs.exists(path, callback)`
- `fs.existsSync(path)`

获取文件信息：

- `fs.stat(path, callback)`
- `fs.stat(path)`

删除文件：

- `fs.unlink(path, callback)`
- `fs.unlinkSync(path)`

列出文件：

- `fs.readdir(path[,options], callback)`
- `fs.readdirSync(path[, options])`

截断文件：

- `fs.truncate(path, len, callback)`
- `fs.truncateSync(path, len)`

建立目录：

- `fs.mkdir(path[, mode], callback)`
- `fs.mkdirSync(path[, mode])`

删除目录：

- `fs.rmdir(path, callback)`
- `fs.rmdirSync(path)`

重命名文件和目录：

- `fs.rename(oldPath, newPath, callback)`
- `fs.renameSync(oldPath, newPath)`

监视文件更改：

- `fs.watchFile(filename[, options], listener)`



## 3 path 路径模块

path 模块是 Node.js 官方提供的、用来处理路径的模块。提供一系列的方法和属性，用来满足对路径的处理需求
如果要在 JavaScript 代码中，使用 path 模块来处理路径，则需要使用如下的方式先导入它

```
const path = require("path")
```

### 3.1 路径拼接 `path.join()`

`path.join(...paths)` 可以把多个路径片段拼接为完整的路径字符串
●**…paths ** 路径片段的序列
● 返回
注意：凡是涉及到路径拼接的操作，都要使用 path.join() 方法进行处理。不直接使用 + 进行字符串的拼接

```js
const path = require('path')
const fs = require('fs')

// 注意：  ../ 会抵消前面的路径
const pathStr = path.join('/a', '/b/c', '../../', './d', 'e')
console.log(pathStr)  // \a\b\d\e

// 取代fs.readFile(__dirname + '/files/1.txt', ...)
fs.readFile(path.join(__dirname, './files/1.txt'), 'utf8', function(err, dataStr) {
  if (err) {
    return console.log(err.message)
  }
  console.log(dataStr)
})

```

![image-20230312102030543](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121020576.png)

![image-20230312102040055](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121020101.png)

### 3.2 获取路径中文件名 `path.basename()`

`path.basename(path[, ext])` 可以获取路径中的最后一部分，经常通过这个方法获取路径中的文件名
●**path** 必选参数，表示一个路径的字符串
●**ext** 可选参数，表示文件扩展名
●返回 路径的最后一部分

```js
const path = require('path')

// 定义文件的存放路径
const fpath = '/a/b/c/index.html'

const fullName = path.basename(fpath)
console.log(fullName) // index.html

const nameWithoutExt = path.basename(fpath, '.html')
console.log(nameWithoutExt) // index

```

![image-20230312102203511](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303121022543.png)

### 3.3 获取路径中文件扩展名 `path.extname()`

`path.extname(path)` 可以获取路径中的扩展名部分
●path 必选参数，表示一个路径的字符串
●返回 返回得到的扩展名字符串

```js
const path = require('path')

// 这是文件的存放路径
const fpath = '/a/b/c/index.html'

const fext = path.extname(fpath)
console.log(fext)//输出.html

```

注意点：

fs.writeFile()方法只能用来创建文件，不能用来创建路径
重复调用fs.writeFile(写入同一个文件，新写入的内容会覆盖之前的旧内容

## 4 HTTP模块

http 模块是 Node.js 官方提供的用来创建 web 服务器的模块

通过 http 模块提供的 `http.createServer()` 方法，就能方便的把一台普通的电脑，变成一台 Web 服务器，从而对外提供 Web 资源服务。

在 Node.js 中，不需要使用 IIS、Apache（针对php） 等第三方 web 服务器软件（普通的电脑常常安装这些），而是基于 Node.js 提供的 http 模块，通过几行简单的代码，就能轻松的手写一个服务器软件，从而对外提供 web 服务

导入 http 模块创建 Web 服务器：

```

```

#### 服务器相关的概念

```
就是互联网上每台计算机的唯一地址，因此 IP 地址具有唯一性。如果把“个人电脑”比作“一台电话”，那么“IP地址”就相当于“电话号码”，只有在知道对方 IP 地址的前提下，才能与对应的电脑之间进行数据通信。
IP 地址的格式：通常用“点分十进制”表示成（a.b.c.d）的形式，其中，a,b,c,d都是 0~255 之间的十进制整数。例如：用点分十进表示的 IP地址（192.168.1.1）
注意
●互联网中每台 Web 服务器，都有自己的 IP 地址，如：可以在 Windows 的终端中运行 ping www.baidu.com 命令，即可查看到百度服务器的 IP 地址
●在开发期间，自己的电脑既是一台服务器，也是一个客户端，为了方便测试，可以在自己的浏览器中输入 127.0.0.1 这个 IP 地址，就能把自己的电脑当做一台服务器进行访问了

```

#### 域名和域名服务器

```
尽管 IP 地址能够唯一地标记网络上的计算机，但IP地址是一长串数字，不直观，而且不便于记忆，于是人们又发明了另一套字符型的地址方案，即所谓的域名（Domain Name）地址。
IP地址和域名是一一对应的关系，这份对应关系存放在一种叫做域名服务器(DNS，Domain name server)的电脑中。使用者只需通过好记的域名访问对应的服务器即可，对应的转换工作由域名服务器实现。因此，域名服务器就是提供 IP 地址和域名之间的转换服务的服务器。
注意
●单纯使用 IP 地址，互联网中的电脑也能够正常工作。但是有了域名的加持，能让互联网的世界变得更加方便
●在开发测试期间， 127.0.0.1对应的域名是 localhost，都代表自己的这台电脑，在使用效果上没有任何区别

```

#### 端口号

```
计算机中的端口号，就好像是现实生活中的门牌号一样。通过门牌号，外卖小哥可以在整栋大楼众多的房间中，准确把外卖送到你的手中。
同样的道理，在一台电脑中，可以运行成百上千个 web 服务。每个 web 服务都对应一个唯一的端口号。客户端发送过来的网络请求，通过端口号，可以被准确地交给对应的 web 服务进行处理。
注意
●每个端口号不能同时被多个 web 服务占用
●在实际应用中，URL 中的 80 端口可以被省略
```

#### 创建最基本的 web 服务器

**创建 web 服务器的基本步骤**

```js
// 1. 导入 http 模块
const http = require('http')

// 2. 创建 web 服务器实例
const server = http.createServer()

// 3. 为服务器实例绑定 request 事件，监听客户端的请求
server.on('request', function (req, res) {
  console.log('Someone visit our web server.')
})

// 4. 启动服务器
server.listen(8080, function () {  
  console.log('server running at http://127.0.0.1:8080')
})

```

VSCODE打开终端，执行命令

![image-20230312135541275](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312135541275.png)

浏览器访问http://127.0.0.1:8080/

终端查看显示

![image-20230312135746116](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312135746116.png)



#### req请求对象

只要服务器接收到了客户端的请求，就会调用通过 server.on() 为服务器绑定的 request 事件处理函数

#### res响应对象

在服务器 request 事件处理函数中，如果想访问与服务器相关的数据或属性，通过`res.end(data）` 方法响应。

```js
server.on('request', (req, res) => {
  // req.url 是客户端请求的 URL 地址
  const url = req.url
  // req.method 是客户端请求的 method 类型
  const method = req.method
  const str = `Your request url is ${url}, and request method is ${method}`
  console.log(str)
  
  // 调用 res.end() 方法，向客户端响应一些内容
  res.end(str)
})

```

![image-20230312140132885](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312140132885.png)

浏览器客户端反馈信息

![image-20230312140323143](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312140323143.png)



#### 解决中文乱码问题

当调用 res.end() 方法，向客户端发送中文内容的时候，会出现乱码问题，此时，需要手动设置内容的编码格式

```js
// 1. 导入 http 模块
const http = require('http')

// 2. 创建 web 服务器实例
const server = http.createServer()

// 3. 为服务器实例绑定 request 事件，监听客户端的请求
server.on('request', (req, res) =>{
  // 定义一个字符串，包含中文的内容
  const str = `您请求的 URL 地址是 ${req.url}，请求的 method 类型为 ${req.method}`
  
  // 调用 res.setHeader() 方法，设置 Content-Type 响应头，解决中文乱码的问题
  res.setHeader('Content-Type', 'text/html; charset=utf-8')
  
  // res.end() 将内容响应给客户端
  res.end(str)
})

// 4. 启动服务器
server.listen(80, ()=> {  
    console.log('server running at http://127.0.0.1')
})

```

![image-20230312140724277](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312140724277.png)

#### 实例

根据不同的 url 响应不同的 html 内容

步骤

1 获取请求的 url地址
2 设置默认的响应内容为 404 Not found
3 判断用户请求的是否为 / 或 /index.html 首页
4 判断用户请求的是否为 /about.html 关于页面
5 设置 Content-Type 响应头，防止中文乱码
6 使用 res.end() 把内容响应给客户端

```js
const http = require('http')
const server = http.createServer()

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

server.listen(80, () => {
  console.log('server running at http://127.0.0.1')
})


```



## 5 模块化

### 5.1 模块基本概念

**模块化**：是指解决一个复杂问题时，**自顶向下逐层把系统划分成若干模块的过程**。对于整个系统来说，模块是可组合、分解和更换的单元
编程领域中的模块化，就是遵守固定的规则，把一个大文件拆成独立并**互相依赖的**多个小模块。
把代码进行模块化拆分的好处

- 提高了代码的复用性
- 提高了代码的可维护性
- 可以实现按需加载

**模块化规范**：就是对代码进行模块化的拆分与组合时，需要遵守的那些规则。例如使用什么样的语法格式来引用模块，在模块中使用什么样的语法格式向外暴露成员
**模块化规范的好处**：大家都遵守同样的模块化规范写代码，降低了沟通的成本，极大方便了各个模块之间的相互调用，利人利己

### 5.2 Node.js 中的模块化

#### 5.2.1 模块的分类

Node.js 中根据模块来源的不同，将模块分为了 3 大类

- 内置模块（内置模块是由 Node.js 官方提供的，例如 fs、path、http 等）
- 自定义模块（用户创建的每个 .js 文件，都是自定义模块）
- 第三方模块（由第三方开发出来的模块，使用前**需要先下载**）

#### 5.2.2 加载模块

使用强大的 `require()`方法，可以加载需要的内置模块、用户自定义模块、第三方模块进行使用
●注意：使用 require() 方法加载其它模块时，会**执行**被加载模块中的代码

```
const fs = require('fs')							// 内置模块
const custom = require('./custom.js')	// 自定义模块，需指明路径，可以省略 .js 的后缀名
const moment = require('moment')			// 第三方模块
```

注意，不用.js后缀名也可以加载对应的文件。

#### 5.2.3.Node.js 中的模块作用域

**模块作用域**：和函数作用域类似，在自定义模块中定义的变量、方法等成员，只能在当前模块内被访问，这种模块级别的访问限制
●模块作用域的好处：防止了全局变量污染的问题

```js
// 模块作用域
const username = '张三'

function sayHello() {
  console.log('大家好，我是' + username)
}
```

```js
const custom = require('./模块作用域')

console.log(custom)

```

![image-20230312144551265](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312144551265.png)

![image-20230312144537729](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312144537729.png)

输出是空对象，说明模块内定义的变量只能在模块内被访问

#### 5.2.4  模块作用域的成员

**1 module对象**

在每个 .js 自定义模块中都有一个module对象，它里面存储了和当前模块有关的信息

```
console.log(module)
```

![image-20230312145022229](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312145022229.png)

2 **module.exports 对象**

●在自定义模块中，可以使用module.exports对象，将模块内的成员共享出去，供外界使用
●外界用 require() 方法导入自定义模块时，得到的就是 module.exports 所指向的对象，而一般默认该属性是{}即空对象。

```
// 在一个自定义模块中，默认情况下， module.exports = {}

const age = 20

// 向 module.exports 对象上挂载 username 属性
module.exports.username = 'zs'
// 向 module.exports 对象上挂载 sayHello 方法
module.exports.sayHello = function() {
  console.log('Hello!')
}
module.exports.age = age//再共享出去

// 让 module.exports 指向一个全新的对象
module.exports = {
  nickname: '小黑',
  sayHi() {
    console.log('Hi!')
  }
}

```

```
// 在外界使用 require 导入一个自定义模块的时候，得到的成员，
// 就是 那个模块中，通过 module.exports 指向的那个对象
const m = require('./11.自定义模块')

console.log(m)

```

![image-20230312150002704](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312150002704.png)

![image-20230312150017224](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312150017224.png)

![image-20230312150033111](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312150033111.png)

3 exports 对象

![image-20230312150717035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312150717035.png)

```
console.log(exports)

console.log(module.exports)

console.log(exports== module.exports)
```

![image-20230312151350936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312151350936.png)



#### 5.2.5 exports 和 module.exports 的使用误区

- 由于 module.exports 单词写起来比较复杂，为了简化向外共享成员的代码，Node 提供了 exports 对象。默认情况下，exports 和 module.exports 指向同一个对象。最终共享的结果，还是以 module.exports 指向的对象为准。
- 时刻谨记，require() 模块时，得到的永远是 module.exports 指向的对象，若出现exports 和 module.exports，最终不管exports怎么指向，都输出module.exports。注意挂载属性和指向新对象的区别。
- 注意：为了防止混乱，建议大家不要在同一个模块中同时使用 exports 和 module.exports

![image-20230312202446859](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122024048.png)

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122025002.png)

#### 5.2.6 Node.js 中的模块化规范

Node.js 遵循了 CommonJS 模块化规范，CommonJS 规定了模块的特性和各模块之间如何相互依赖
●每个模块内部，module 变量代表当前模块
●module 变量是一个对象，它的 exports 属性（即 module.exports）是对外的接口
●加载某个模块，其实是加载该模块的 module.exports 属性。require() 方法用于加载模块

### 5.3 npm与包

#### 5.3.1 包的概念和下载

Node.js 中的第三方模块又叫做包

不同于 Node.js 中的内置模块与自定义模块，包是由第三方个人或团队开发出来的，免费供所有人使用。Node.js 中的包都是免费且开源的

##### 1 为什么需要包？

- 由于 Node.js 的内置模块仅提供了一些底层的 API，导致在基于内置模块进行项目开发的时，效率很低
- 包是基于内置模块封装出来的，提供了更高级、更方便的 API，极大的提高了开发效率
- 包和内置模块之间的关系，类似于 jQuery和 浏览器内置 API 之间的关系

##### 2  哪儿下载包

国外有一家 IT 公司，叫做 npm, Inc. 这家公司旗下有一个非常著名的网站 https://www.npmjs.com/，它是全球最大的包共享平台，你可以从这个网站上搜索到任何你需要的包，只要你有足够的耐心！到目前位置，全球约1100多万的开发人员，通过这个包共享平台，开发并共享了超过120多万个包 供我们使用。npm, Inc. 公司提供了一个地址为 https://registry.npmjs.org/ 的服务器，来对外共享所有的包，我们可以从这个服务器上下载自己所需要的包。

1、从 https://www.npmjs.com/网站上搜索自己所需要的包
2、从 https://registry.npmjs.org/ 服务器上下载自己需要的包

![image-20230312220620413](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122206724.png)

##### 3 如何下载包

npm, Inc. 公司提供了一个包管理工具，使用这个工具从 https://registry.npmjs.org/ 服务器把需要的包下载到本地使用。

工具的名字叫做 Node Package Manager（简称 npm 包管理工具），这个包管理工具随着 Node.js 的安装包一起被安装到了用户的电脑上。在终端中

执行 npm -v命令，来查看自己电脑上所安装的 npm 包管理工具的版本号

![image-20230312203508167](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122035206.png)

#### 5.3.2  NPM初体验

格式化时间的传统做法

```
// dateFormat.js

// 1. 定义格式化时间的方法
function dateFormat(dtStr) {
  const dt = new Date(dtStr)

  const y = dt.getFullYear()
  const m = padZero(dt.getMonth() + 1)
  const d = padZero(dt.getDate())
  const hh = padZero(dt.getHours())
  const mm = padZero(dt.getMinutes())
  const ss = padZero(dt.getSeconds())

  return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
}

// 定义补零的函数
function padZero(n) {
  return n > 9 ? n : '0' + n
}

module.exports = {
  dateFormat
}

```

```
// 导入自定义的格式化时间的模块
const TIME = require('./15dateformat')

// 调用方法，进行时间的格式化
const dt = new Date()
// console.log(dt)
const newDT = TIME.dateFormat(dt)
console.log(newDT)

```

![image-20230312211941336](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122119399.png)

![image-20230312211957182](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122119238.png)

格式化时间的高级做法

```
// 1. 导入需要的包
// 注意：导入的名称，就是装包时候的名称
const moment = require('moment')
//查文档看用法
const dt = moment().format('YYYY-MM-DD HH:mm:ss')//对时间进行格式化
console.log(dt)

```

##### 1  在项目中安装包的命令

下载在项目里

```
npm install 包的完整名称
或者
npm i 包的完整名称
npm i 包的完整名称 包的完整名称（加空格可以安装多个包）

# 例子
npm i moment

```

![image-20230312220225435](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122202482.png)

![image-20230312220338078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122203122.png)

![image-20230312220650953](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122206023.png)

![image-20230312220736015](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122207091.png)

```
// 1. 导入需要的包
// 注意：导入的名称，就是装包时候的名称
const moment = require('moment')
//查文档看用法
const dt = moment().format('YYYY-MM-DD HH:mm:ss')//对时间进行格式化
console.log(dt)
```

![image-20230312223450539](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122234595.png)

初次装包完成后，在项目文件夹下多一个叫做 node_modules 的文件夹和 package-lock.json 的配置文件。

- node_modules 文件夹用来存放所有已安装到项目中的包。require() 导入第三方包时，从这个目录中查找并加载
- package-lock.json 配置文件用来记录 node_modules 目录下的每一个包的下载信息，例如包的名字、版本号、下载地址等

![image-20230312223836590](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122238659.png)

**注意**：不要手动修改 node_modules 或 package-lock.json文件中的任何代码，npm 包管理工具会自动维护它们

##### 2 安装指定版本的包

npm install 命令默认安装最新版本的包。如需安装指定版本的包，在包名之后，@

```
npm i 包的完整名称@版本号
```

不用删除以前的版本，npm会自动覆盖之前的版本。

重新安装moment，指定版本2.24.0

![image-20230312224458315](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122244364.png)

发现版本变了

![image-20230312224517266](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303122245331.png)



##### 3 版本号

包的版本号以“点分十进制”形式进行定义，总共有三位数字，如 2.24.0。其中每一位数字所代表的的含义如下

第1位数字：大版本
第2位数字：功能版本
第3位数字：Bug修复版本
版本号提升的规则：只要前面的版本号增长了，则后面的版本号归零

#### 5.3.3包管理配置文件

npm规定，**在项目根目录中**，必须提供一个叫做 package.json 的包管理配置文件。用来记录与项目有关的一些配置信息。如

- 项目的名称、版本号、描述等
- 项目中都用到了哪些包
- 哪些包只在开发期间会用到
- 那些包在开发和部署时都需要用到

##### 1、多人协作的问题

遇到的问题：第三方包的体积过大，不方便团队成员之间共享项目源代码。

解决方案：共享时剔除 node_modules



##### 2、如何记录项目中安装了哪些包

在项目根目录 package.json 的配置文件用来记录项目中安装了哪些包。从而方便剔除 node_modules 目录之后，在团队成员之间共享项目的源代码

注意：今后在项目开发中，一定要把 node_modules文件夹，添加到 .gitignore 忽略文件中

##### 3、快速创建 package.json

npm 包管理工具提供了一个快捷命令，可以在执行命令时所处的目录中，快速创建 package.json 这个包管理配置文件。如下：

```
npm init -y
```

在新建项目文件夹以后就执行该命令，项目开发期间只用执行这一次就行。

- 上述命令只能在英文的目录下成功运行！所以项目文件夹的名称一定要使用英文命名，不能出现空格
- 运行 npm install 命令安装包的时候，npm包管理工具会自动把包的名称和版本号，记录到 package.json
- package.json 文件中的 **dependencies** 节点，专门用来记录您使用 npm install 命令安装了哪些包

![image-20230313183630651](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131836834.png)

文件目录下自动生成了

![image-20230313183656026](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131836070.png)

![image-20230313183939646](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131839702.png)

执行npm install 命令后

![image-20230313185856175](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131858246.png)

安装多个包

```
npm i jquery art-template
```

![image-20230313190052464](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131900530.png)

##### 4、一次性安装所有的包

拿到一个剔除了 node_modules 的项目之后，需要先把所有的包下载到项目中，才能将项目运行起来。否则会报类似于下面的错误

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131913252.png)

可以运行 `npm install` 命令（或 npm i）一次性安装所有的依赖包

```
npm install
```

##### 5、卸载包

运行 `npm uninstall` 命令，来卸载指定的包

```
npm uninstall 包名
```

npm uninstall [命令执行](https://so.csdn.net/so/search?q=命令执行&spm=1001.2101.3001.7020)成功后，会把卸载的包，自动从 package.json 的 dependencies 中移除掉

6、**devDependencies节点**

如果某些包只在项目开发阶段会用到，在项目上线之后不会用到，则建议把这些包记录到 devDependencies节点中。如果某些包在开发和项目上线之后都需要用到，则建议把这些包记录到 dependencies 节点中。可以使用如下的命令，将包记录到 devDependencies节点中。

```
//完整写法 包名和--save-dev顺序不重要
npm install 包名 --save-dev     
或
//常用简写
npm i 包名 -D

```

那如何判断是否需要加-D，可以查看包的安装文档

![image-20230313192252617](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131922670.png)

![image-20230313192431694](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131924766.png)

#### 5.3.4 **解决下包速度慢的问题**

1. npm 下包的时候，默认从国外的 https://registry.npmjs.org/ 服务器进行下载，可能会慢
2. 使用国内镜像服务器-淘宝，大幅改善下载速度

![image-20220316002029652](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131925029.png)

##### 1 切换npm 的下包镜像源

```
npm config get registry 	# 查看当前包镜像源
npm config set registry=http://registry.npm.taobao.org/ # 切换源头
npm config get registry 	# 检查镜像源是否下载成功
```

![image-20230313193531335](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303131935383.png)



为了方便的切换下包的镜像源，安装**nrm**小工具，利用 nrm 提供的终端命令，快速查看和切换下包的镜像源

```
# 安装小工具
npm install nrm -g	# -g 全局可用
nrm ls							# 查看当前可用的镜像源地址list
nrm use taobao			# 切换镜像源
```

![image-20230316130523981](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161305155.png)

![image-20230316133104319](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161331425.png)

##### 2 包的分类

使用 npm 包管理工具下载的包，共分为两大类，分别是

1、项目包

那些被安装到项目的 node_modules 目录中的包，都是项目包。项目包又分为两类

- 开发依赖包（被记录到 devDependencies节点中的包，只在开发期间会用到）npm i 包名 -D
- 核心依赖包（被记录到 dependencies节点中的包，在开发期间和项目上线之后都会用到）npm i 包名

2、全局包

在执行 npm install 命令时，如果提供了 -g 参数，则会把包安装为全局包

全局包会被安装到 C:\Users\用户目录\AppData\Roaming\npm\node_modules 目录下

![image-20230316134806909](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161348971.png)

```
npm install 包名 -g	# 全局安装指定的包
npm uninstall 包名 -g	# 卸载全局指定的包

```

注意

- **只有工具性质的包，才有全局安装的必要性**。因为它们提供了好用的终端命令
- 判断某个包是否需要全局安装后才能使用，可以参考官方提供的使用说明即可

##### 3 i5ting_toc

i5ting_toc 是一个可以把 md 文档转为 html 页面的小工具

![image-20230316135524298](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161355373.png)

```
npm i i5ting_toc -g						# 安装
i5ting_toc -f sample.md -o		# 转换
```

##### 4 规范的包结构

一个规范的包，它的组成结构，必须符合以下 3 点要求

- 包必须以单独的目录而存在

- 包的顶级目录（点进去的目录）下要必须包含 package.json 这个包管理配置文件

- package.json 中必须包含 name，version，main这三个属性，分别代表包的名字、版本号、包的入口(.js文件)（require()加载的文件）

  ![image-20230316135819497](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161358559.png)

  关于更多的约束参考 https://classic.yarnpkg.com/en/package/package-json

![image-20230316135929759](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161359833.png)

##### 5 开发包

1新建 itjiang-tools 文件夹，作为包的根目录
2在 itjiang-tools 文件夹中，新建。

![image-20230316140959613](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161409665.png)

**也可以直接初始化（npm init -y）**

●package.json （包管理配置文件）

```
{
  "name": "itjiang-tools",
  "version": "1.1.0",
  "main": "index.js",
  "description": "提供了格式化时间、HTMLEscape相关的功能",//检索时出现的功能介绍
  "keywords": [//搜索关键词
    "itjiang",
    "dateFormat"
  ],
  "license": "ISC"//协议
}

```

●index.js （包的入口文件）

```js
//这是包的入口文件

//定义时间格式化的函数
function dateFormat(dtStr) {
  const dt = new Date(dtStr)

  const y = dt.getFullYear()
  const m = padZero(dt.getMonth() + 1)
  const d = padZero(dt.getDate())
  const hh = padZero(dt.getHours())
  const mm = padZero(dt.getMinutes())
  const ss = padZero(dt.getSeconds())

  return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
}

// 定义补零的函数
function padZero(n) {
  return n > 9 ? n : '0' + n
}

module.exports = {
  dateFormat
}

```

●README.md （包的说明文档）

````
## 安装
```
npm install itjiang-tools
```

## 导入
```js
const itjiang = require('itjiang-tools')
```

## 格式化时间
```js
// 调用 dateFormat 对时间进行格式化
const dtStr = itjiang.dateFormat(new Date())
// 结果  2020-04-03 17:20:58
console.log(dtStr)
```


## 开源协议
ISC

````

![image-20230316143238113](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161432166.png)

测试结果：

```js
const itjiang = require('./itjiang-tools')

// 格式化时间的功能
const dtStr = itjiang.dateFormat(new Date())

console.log(dtStr)
```

![image-20230316150536618](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161505687.png)

调整目录，新建SRC目录

![image-20230316151219750](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161512802.png)

![image-20230316151202262](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161512333.png)

```js
//定义时间格式化的函数
function dateFormat(dtStr) {
    const dt = new Date(dtStr)
  
    const y = dt.getFullYear()
    const m = padZero(dt.getMonth() + 1)
    const d = padZero(dt.getDate())
    const hh = padZero(dt.getHours())
    const mm = padZero(dt.getMinutes())
    const ss = padZero(dt.getSeconds())
  
    return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
  }
  
  // 定义补零的函数
  function padZero(n) {
    return n > 9 ? n : '0' + n
  }

  module.exports = {
    dateFormat
  }
```

修改入口文件index.js

```js
// src 文件夹下开发代码，导入到 index.js 中
const date = require('./src/dateFormat')

module.exports = {
...date,		// ... 展开运算符，将data所有属性交给新对象
}
```

![image-20230316151515177](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161515236.png)

重新测试

![image-20230316152433136](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161524195.png)

##### 6 发布包

https://www.npmjs.com/ 注册 npm 账号

![image-20230316152536846](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161525904.png)

![image-20230316152853583](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161528651.png)

在终端登录，终端中执行 npm login 命令，依次输入用户名、密码、邮箱后，即可登录成功

出现问题

![image-20230316153210493](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161532546.png)

![image-20230316153601121](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303161536175.png)

```
npm config set registry https://registry.npmjs.org
```

 ●注意：执行命令前，必须先把下包的服务器地址切换为 npm 的官方服务器。否则会导致发布包失败！先用nrm命令检查一下，nrm use 命令切换。

终端切换到包的根目录之后，运行 npm publish 命令，即可将包发布到 npm 上（注意：包名不能雷同）

运行 npm unpublish 包名 --force命令，即可从 npm 删除已发布的包
●npm unpublish 命令只能删除 72 小时以内发布的包
●npm unpublish 删除的包，在 24 小时内不允许重复发布
●发布包的时候要慎重，尽量不要往 npm 上发布没有意义的包

### 5.4 模块的加载机制

模块在第一次加载后会被缓存，多次调用 require() 模块的代码只会被执行一次。不论是内置模块、用户自定义模块、还是第三方模块，它们都会优先从缓存中加载，从而提高模块的加载效率。

#### 5.4.1 **内置模块的加载机制**

 ●内置模块的加载优先级最高（当第三方模块和内置模块同名时）

#### 5.4.2 **自定义模块的加载机制**

●使用 require() 加载自定义模块时，必须指定以 ./ 或 …/ 开头的路径标识符。在加载自定义模块时，如果没有指定 ./ 或 …/ 这样的路径标识符，则 node 会把它当作内置模块或第三方模块进行加载。

●在使用 require() 导入自定义模块时，如果省略了文件的扩展名，Node.js 会按顺序分别尝试加载以下的文件

 1 按照确切的文件名进行加载
 2 补全 .js 扩展名进行加载
 3 补全 .json 扩展名进行加载
 4 补全 .node 扩展名进行加载
 5 加载失败，终端报错

#### 5.4.3 **第三方模块的加载机制**

 ●如果传递给 require() 的模块标识符不是一个内置模块，也没有以 ./ 或 …/ 开头，则 Node.js 会从当前模块的父目录开始，尝试从 /node_modules 文件夹中加载第三方模块
 ●如果没有找到对应的第三方模块，则移动到再上一层父目录中，进行加载，直到文件系统的根目录
 ●假设在 ‘C:\Users\itheima\project\node_modules\a.js’ 里调用 require(‘tools’)，Node.js 会按以下顺序查找

C:\Users\itheima\project\node_modules\tools
C:\Users\itheima\node_modules\tools
C:\Users\node_modules\tools
C:\node_modules\tools
报错

#### 5.4.4 **目录作为模块**

●当把目录作为模块标识符，传递给 require() 进行加载的时候，有三种加载方式

1 在被加载的目录下查找一个叫做 package.json 的文件，并寻找 main 属性值作为 require() 加载的入口
2 如果目录里没有 package.json 文件，或者 main 入口不存在或无法解析，则 Node.js 将会试图加载目录下的 index.js 文件
3 如果以上两步都失败了，则 Node.js 会在终端打印错误消息，报告模块的缺失：Error: Cannot find module ‘xxx’

## 6 Express 

### 6.1 Express 简介

Express 是基于 Node.js 平台，快速、开放、极简的 Web 开发框架
通俗的理解：Express 的作用和 Node.js 内置的 http 模块类似，是专门用来创建 Web 服务器的。
本质就是一个 npm 上的第三方包，提供了快速创建 Web 服务器的便捷方法
中文官网 http://www.expressjs.com.cn/

```
不使用 Express 能否创建 Web 服务器？
能，使用 Node.js 提供的原生 http 模块即可
有了 http 内置模块，为什么还有用 Express？
http 内置模块用起来很复杂，开发效率低；
Express 是基于内置的 http 模块进一步封装出来的，能够极大的提高开发效率
http 内置模块与 Express 是什么关系？
类似于浏览器中 Web API 和 jQuery 的关系。后者是基于前者进一步封装出来的

```

对于前端程序员来说，最常见的两种服务器，分别是
Web 网站服务器：专门对外提供 Web 网页资源的服务器
API 接口服务器：专门对外提供 API 接口的服务器
使用 Express，我们可以方便、快速的**创建 Web 网站的服务器或 API 接口的服务器**



### 6.2 基本使用

#### 6.2.1 安装 Express：

```
npm i express@4.17.1
```

#### 6.2.2 创建基本的 Web 服务器

```js
const express = require('express');
                        
const app = express();							// 创建web服务器

app.listen(80, () => {							// 调用回调函数，listen() 启动服务器
	console.log('express server running at http://127.0.0.1')
});

```

![image-20230316211935434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303162119583.png)

#### 6.2.3 处理请求

```
// 监听 GET 请求
app.get('url', function(req, res) { /*处理函数*/ })

// 监听 POST 请求
app.post('url', function(req, res) { /*处理函数*/ })

app.post('url', function(req, res) {
  // 通过 req.query 对象，可以访问到客户端通过查询字符串的形式，发送到服务器的参数
  console.log(req.query)
  
  // 通过 req.params 对象，可以访问到 URL 中，通过 : 匹配到的动态参数
  console.log(req.parms)
  
  // 通过 res.send()方法，把处理好的内容发送给客户端
  res.send(data);
})


```

```js
// 1. 导入 express
const express = require('express')
// 2. 创建 web 服务器
const app = express()

// 4. 监听客户端的 GET 和 POST 请求，并向客户端响应具体的内容
app.get('/user', (req, res) => {
  // 调用 express 提供的 res.send() 方法，向客户端响应一个  JSON 对象
  res.send({ name: 'zs', age: 20, gender: '男' })
})
app.post('/user', (req, res) => {
  // 调用 express 提供的 res.send() 方法，向客户端响应一个  文本字符串
  res.send('请求成功')
})
app.get('/', (req, res) => { 
  // 通过 req.query 可以获取到客户端发送过来的 查询参数 ‘/’表示ip根目录
  // 注意：默认情况下，req.query 是一个空对象
  console.log(req.query)
  res.send(req.query)
})


// 注意：这里的 :id 是一个动态的参数
app.get('/user/:id/:username', (req, res) => {
  // req.params 是动态匹配到的 URL 参数，默认也是一个空对象
  console.log(req.params)
  res.send(req.params)
})

// 3. 启动 web 服务器
app.listen(80, () => {
  console.log('express server running at http://127.0.0.1')
})

```

**注意： :id 是一个动态的参数，‘：’是固定的写法，可以有多个参数，其中id 是该参数名字，而req.params可以动态匹配该参数的值。**

![image-20230316214932405](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303162149482.png)

![image-20230316215001232](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303162150287.png)

#### 6.2.4 静态资源

- express 提供了一个非常好用的函数，叫做 express.static()，通过它，我们可以非常方便地创建一个静态资源服务器，例如，通过如下代码就可以将 public 目录下的图片、CSS 文件、JavaScript 文件对外开放访问了

```
app.use(express.static('public'))
```

 现在，你就可以访问 public 目录中的所有文件了
 http://localhost/images/bg.jpg
 http://localhost/css/style.css
 http://localhost/js/login.js

- 注意：Express 在指定的静态目录中查找文件，对外提供资源访问路径，目录名不会出现在 URL 中

```
const express=require('express')
const path = require('path')

const app=express()

//静态资源处理
//app.use(express.static(path.join(__dirname, './public')))
app.use(express.static('./public'))

app.listen(80,()=>{
  console.log('express server running at http://127.0.0.1')

})
```

![image-20230316222057360](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303162220428.png)

托管多个静态资源

- 访问静态资源文件时，express.static() 函数会根据目录的添加顺序查找所需的文件，如下同名先访问public文件夹

```js
# 按照顺序来查找文件
app.use(express.static('public'))
app.use(express.static('files'))
```

**挂载路径前缀**

- 如果希望在托管的静态资源访问路径之前，挂载路径前缀，则可以使用如下的方式

```
app.use('/public', express.static('public'))
```

 现在，你就可以通过带有 /public 前缀地址来访问 public 目录中的文件了
 http://localhost:3000/public/images/kitten.jpg
 http://localhost:3000/public/css/style.css
 http://localhost:3000/public/js/app.js

![image-20230317133726710](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171337847.png)

![image-20230317133737623](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171337690.png)



```js
const express = require('express')
const app = express()

// 在这里，调用 express.static() 方法，快速的对外提供静态资源
app.use('/abc',express.static('./public'))

app.listen(80, () => {
  console.log('express server running at http://127.0.0.1')
})

```

#### 6.2.5 安装nodemon

在编写调试 Node.js 项目的时候，如果修改了项目的代码，需要频繁的手动重新启动服务.

使用 nodemon https://www.npmjs.com/package/nodemon 工具，它能够监听项目文件的变动，当代码被修改后，nodemon 会自动重启项目，极大方便了开发和调试。

```
npm i -g nodemon
```

![image-20230317133908842](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171339906.png)

现在，我们可以将 node 命令替换为 **nodemon** 命令，使用 nodemon app.js 来启动项目。

相当于热启动，不用我们自己手动重新启动

```
nodemon app.js
```

问题：

![image-20230317134203955](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171342019.png)

解决办法：

```
npx nodemon .\test_static.js
```

![image-20230317134420654](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171344718.png)





### 6.3  Express 路由

#### 6.3.1 Express 路由简介

现实中的路由

![image-20230317134937670](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171349799.png)

广义上来讲，路由就是映射关系

![image-20230317135024672](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171350729.png)

在 Express 中，路由指的是客户端的请求与服务器处理函数之间的映射关系

- Express 中的路由分 3 部分组成，分别是请求的类型、请求的 URL 地址、处理函数，格式如下

```
app.method(path, handler())	// method 具体为 get post 等
```

路由的匹配过程

- 每当一个请求到达服务器之后，需要先经过路由的匹配，只有匹配成功之后，才会调用对应的处理函数。
- 在匹配时，会按照路由的顺序进行匹配，如果请求类型和请求的 URL 同时匹配成功，则 Express 会将这次请求，转交给对应的 function 函数进行处理。

![image-20230317134824821](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171348916.png)

注意

1. 按照定义的先后顺序进行匹配
2. 请求类型和请求的URL同时匹配成功，才会调用对应的处理函数

#### 6.3.2 Express 使用

●在 Express 中使用路由最简单的方式，就是把路由挂载到 app 上，示例代码：

```js
const express = require('express')

const app = express()

// 挂载路由
app.get('/', (req, res) => {res.send('hello world.')})
app.post('/', (req, res) => {res.send('Post Request.')})

app.listen(80, () => {console.log('express server running at http://127.0.0.1')})

```

实践:

```
# 创建一个空文件夹，cd到目录下

# 先初始化
npm init -y

# 引入Express
npm i express@4.17.1

```

![image-20230317151008585](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171510644.png)

![image-20230317151210192](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171512272.png)

测试：

![image-20230317151639897](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171516962.png)

![image-20230317151708676](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171517745.png)

#### 6.3.3 模块化路由

为了方便对路由进行模块化的管理，Express 不建议将路由直接挂载到 app 上，而是推荐将路由抽离为单独的模块。将路由抽离为单独模块的步骤如下

- 创建路由模块对应的 .js 文件
- 调用 express.Router() 函数创建路由对象
- 向路由对象上挂载具体的路由
- 使用 module.exports 向外共享路由对象
- 使用 app.use() 函数注册路由模块

```js
// 路由模块 router.js

const express = require('express')	// 1. 导入 express
const router = express.Router()			// 2. 创建路由对象

// 3. 挂载具体的路由
router.get('/user/list', (req, res) => {res.send('Get user list.')})
router.post('/user/add', (req, res) => {res.send('Add new user.')})

// 4. 向外导出路由对象
module.exports = router

```

```js
const express = require('express')
const router = require('./router')	// 1. 导入路由模块

const app = express()

// 注意： app.use() 函数的作用，就是来注册全局中间件
// app.use('/files', express.static('./files'))
app.use('/api', router)	// 2. 注册路由模块，若想使用静态资源一样可以加统一的访问前缀

app.listen(80, () => {console.log('http://127.0.0.1')})

```

![image-20230317161443329](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171614383.png)



![image-20230317161430364](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171614425.png)



### 6.4 Express中间件

#### 6.4.1 简介

**中间件**（Middleware ），特指业务流程的中间处理环节

生活中的例子

![image-20230317161822912](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171618992.png)

Express 中间件的调用流程

- 当一个请求到达 Express 的服务器之后，可以连续调用多个中间件，从而对这次请求进行预处理

![image-20230317151912020](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171519074.png)

#### 6.4.2 **Express 中间件的格式**

- Express 的中间件，本质上就是一个 **function 处理函数**，Express 中间件的格式如下
- 注意：中间件函数的形参列表中，必须包含 **next 参数**，而路由处理函数中只包含 req 和 res

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171619392.png)

**next 函数的作用**

- next 函数是实现多个中间件连续调用的关键，它表示把流转关系**转交**给下一个中间件或路由

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303171619885.png)

#### 6.4.3 中间件初体验

全局生效的中间件：是客户端发起的任何请求，达到服务器之后，都会触发的中间件

- 通过**app.use(中间件函数)**，即可定义一个全局生效的中间件

```js
const express = require('express')
const app = express()

// 定义一个最简单的中间件函数
//mw指向的就是一个中间件函数
const mw = function (req, res, next) {
  console.log('这是最简单的中间件函数')
  // 把流转关系，转交给下一个中间件或路由
  next()
}

// 将 mw 注册为全局生效的中间件
app.use(mw)

// 这是定义全局中间件的简化形式
// app.use((req, res, next) => {
//   console.log('这是最简单的中间件函数')
//   next()
// })

app.get('/', (req, res) => {
  console.log('调用了 / 这个路由')
  res.send('Home page.')
})
app.get('/user', (req, res) => {
  console.log('调用了 /user 这个路由')
  res.send('User page.')
})

app.listen(80, () => {
  console.log('http://127.0.0.1')
})

```

![image-20230317210345237](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172103380.png)

![image-20230317210400646](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172104736.png)



#### 6.4.4 中间件作用

- 多个中间件之间，共享同一份 req和 res。基于这样的特性，我们可以在上游的中间件中，统一为 req 或 res 对象添加自定义的属性或方法，供下游的中间件或路由进行使用

![image-20230317210609219](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172106295.png)

```js
const express = require('express')
const app = express()

//定义全局中间件的简化形式
app.use((req, res, next) => {
  const time = Date.now()	// 获取到请求到达服务器的时间
  req.startTime = time	
    // 为 req 对象，挂载自定义属性，从而把时间共享给后面的所有路由！！！！！！！！！！
  next()
})

app.get('/', (req, res) => {res.send('Home page.' + req.startTime)})
app.get('/user', (req, res) => {res.send('User page.' + req.startTime)})

app.listen(80, () => {console.log('http://127.0.0.1')})

```

![image-20230317212910144](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172129226.png)

![image-20230317212941300](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172129367.png)



#### 6.4.5 全局中间件

**定义多个全局中间件**，按照顺序先后调用

```js
const express = require('express')
const app = express()

// 定义第一个全局中间件
app.use((req, res, next) => {
  console.log('调用了第1个全局中间件')
  next()
})
// 定义第二个全局中间件
app.use((req, res, next) => {
  console.log('调用了第2个全局中间件')
  next()
})

app.get('/user', (req, res) => {res.send('User page.')})

app.listen(80, () => {console.log('http://127.0.0.1')})

```

![image-20230317213447165](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172134264.png)

#### 6.4.6 局部中间件

**局部生效的中间件**：**不使用 app.use() 定义的中间件**叫做局部生效的中间件

**定义多个局部中间件**

```js
const express = require('express')

const app = express()

// 1. 定义中间件函数
const mw1 = (req, res, next) => {
  console.log('调用了局部生效的中间件')
  next()
}

const mw2 = (req, res, next) => {
  console.log('调用了第二个局部生效的中间件')
  next()
}

// 2. 创建路由，可见mw1，mw2只会在对应有调用的中间件中生效，调用：在get中的url和method中加一个/多个参数
//以下两种方式等价
app.get('/', mw1, mw2, (req, res) => {res.send('Home page.')})
app.get('/user', [mw1, mw2],(req, res) => {res.send('User page.')})

app.listen(80, function () {console.log('Express server running at http://127.0.0.1')})

```

![image-20230317214016594](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172140696.png)

![image-20230317214027193](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172140268.png)

![image-20230317214047774](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172140848.png)

![image-20230317214102464](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172141554.png)

#### 6.4.7 使用注意事项

![image-20230317214158245](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172141323.png)

中间件的5个使用注意事项

- 一定要在路由之前注册中间件，如果直接匹配到路由就会直接响应了。
- 客户端发送过来的请求，可以连续调用多个中间件进行处理
- 执行完中间件的业务代码之后，不要忘记调用 next() 函数
- 为了防止代码逻辑混乱，调用 next() 函数后不要再写额外的代码
- 连续调用多个中间件时，多个中间件之间，共享req 和 res 对象
  

#### 6.4.8 中间件分类

![image-20230317214249437](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172142499.png)

为了方便大家理解和记忆中间件的使用，Express 官方把常见的中间件用法，分成了 5 大类

- 应用级别的中间件
- 路由级别的中间件
- 错误级别的中间件
- Express 内置的中间件
- 第三方的中间件



1 **应用级别的中间件**（就是一种叫法，没啥用）
通过 **app.use() 或 app.get() 或 app.post() ，绑定到 app 实例上的中间件**，叫做应用级别的中间件



2 **路由级别的中间件**
绑定到 **express.Router()** 实例上的中间件，叫做路由级别的中间件。它的用法和应用级别中间件没有任何区别。只不过，应用级别中间件是绑定到 app 实例上，路由级别中间件绑定到 router 实例上

```js
const express = require('express')
const router = express.Router()

// 路由级别的中间件
router.use((req, res, next)=>{
  console.log('Time:' + Date.now())
	next()
})

module.experts = router

```

3 **错误级别的中间件**

错误级别中间件的作用：专门用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题。格式：错误级别中间件的 function 处理函数中，必须有 4 个形参，形参顺序从前到后，分别是 (err, req, res, next)。
注意：错误级别的中间件，必须注册在所有路由之后！

```js
const express = require('express')
const app = express()

// 1. 定义路由
app.get('/', (req, res) => {
  // 1.人为的制造错误
  throw new Error('服务器内部发生了错误！')
  res.send('Home page.')
})

// 2. 定义错误级别的中间件，捕获整个项目的异常错误，从而防止程序的崩溃
app.use((err, req, res, next) => {
  console.log('发生了错误！' + err.message)
  res.send('Error：' + err.message)
})

app.listen(80, function () {console.log('Express server running at http://127.0.0.1')})

```

![image-20230317214808875](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172148975.png)



![image-20230317214757491](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172147562.png)

4 Express内置的中间件

自 Express 4.16.0 版本开始，Express 内置了 3 个常用的中间件，极大提高了 Express 项目的开发效率和体验

- express.static() 快速托管静态资源的内置中间件，例如： HTML 文件、图片、CSS 样式等（无兼容性，任何版本都能用）
- express.json() 解析 JSON 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）
- express.urlencoded(option) 解析 URL-encoded 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）
  

```js
const express = require('express')
const app = express()

// 通过 express.json() 这个中间件，解析表单中的 JSON 格式的数据
app.use(express.json())

// 通过 express.urlencoded() 这个中间件，来解析 表单中的 url-encoded 格式的数据
app.use(express.urlencoded({ extended: false }))

app.post('/user', (req, res) => {
  // 在服务器端，可以通过 req.body 来获取 JSON 格式的表单数据和 url-encoded 格式的请求体数据
  // 默认情况下，如果不配置解析表单数据的中间件，则 req.body 默认等于 undefined
  console.log(req.body)
  res.send('ok')
})

app.post('/book', (req, res) => {
  console.log(req.body)
  res.send('ok')
})

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(80, function () {
  console.log('Express server running at http://127.0.0.1')
})

```

![image-20230317215650163](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172156231.png)

![image-20230317215707274](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172157360.png)

![image-20230317220810503](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172208571.png)

![image-20230317220820983](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172208052.png)



5 第三方的中间件

非 Express 官方内置的，由第三方开发出来的中间件。项目中，可以按需下载并配置第三方中间件，从而提高项目的开发效率

如：在 express@4.16.0 之前的版本中，经常使用 body-parser 这个第三方中间件，来解析请求体数据。使用步骤如下

![image-20230317220959448](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172209513.png)

- 运行 npm install body-parser安装中间件
- 使用 require导入中间件
- 调用 app.use() 注册并使用中间件
  注意：Express 内置的 express.urlencoded，就是基于 body-parser 这个第三方中间件进一步封装出来的

```js
const express = require('express')
const app = express()

// 1. 导入解析表单数据的中间件 body-parser
const parser = require('body-parser')
// 2. 使用 app.use() 注册中间件
app.use(parser.urlencoded({ extended: false }))
// app.use(express.urlencoded({ extended: false }))

app.post('/user', (req, res) => {
  // 如果没有配置任何解析表单数据的中间件，则 req.body 默认等于 undefined
  console.log(req.body)
  res.send('ok')
})

app.listen(80, function () {console.log('Express server running at http://127.0.0.1')})

```

![image-20230317221534596](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172215667.png)

![image-20230317221544500](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172215588.png)

#### 6.4.9 自定义中间件

自己手动模拟一个类似于 express.urlencoded这样的中间件，来解析 POST 提交到服务器的表单数据，实现步骤：

1 定义中间件

2 监听 req 的 data 事件

来获取客户端发送到服务器的数据。如果数据量比较大，无法一次性发送完毕，则客户端会把数据切割后，分批发送到服务器。所以 data 事件可能会触发多次，每一次触发 data 事件时，获取到数据只是完整数据的一部分，需要手动对接收到的数据进行拼接。

3 监听 req 的 end 事件

当请求体数据接收完毕之后，会自动触发 req 的 end 事件，可以在 req 的 end 事件中，拿到并处理完整的请求体数据

Node.js 内置了一个 querystring 模块，专门用来处理查询字符串。通过这个模块提供的 parse() 函数，可以轻松把查询字符串，解析成对象的格式

4 使用 querystring模块解析请求体数据

5 将解析出来的数据对象挂载为 req.body

6 将自定义中间件封装为模块

```js
// custom-body-parser.js

const qs = require('querystring')	// 导入 Node.js 内置的 querystring 模块

const bodyParser = (req, res, next) => {
  // 定义中间件具体的业务逻辑
  // 1. 定义一个 str 字符串，专门用来存储客户端发送过来的请求体数据
  let str = ''
  // 2. 监听 req 的 data 事件，str 中存放的是完整的请求体数据
  req.on('data', (chunk) => {
    str += chunk
  })
  // 3. 监听 req 的 end 事件（请求体发送完毕后自动触发）
  req.on('end', () => {
    req.body = qs.parse(str)	// 把字符串格式的请求体数据，解析成对象格式，不解析的话是 name=zs&gender=%6Eksskk
      //将解析出来的数据挂载在req.body上，供下游中间件访问
    next()
  })
}

module.exports = bodyParser

```

```js
const express = require('express')
const app = express()

// 导入自己封装的中间件模块，将自定义的中间件函数，注册为全局可用的中间件
app.use(require('./custom-body-parser'))

app.post('/user', (req, res) => {
  res.send(req.body)
})

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(80, function () {
  console.log('Express server running at http://127.0.0.1')
})

```

![image-20230317222315042](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172223121.png)

### 6.5 Express编写接口

```js
// apiRouter.js

const express = require('express')
const router = express.Router()

// GET 接口
router.get('/get', (req, res) => {
  // 通过 req.query 获取客户端发送到服务器的数据
  const query = req.query
  res.send({
    status: 0,						// 0 表示处理成功，1 表示处理失败
    msg: 'GET 请求成功！',	// 状态的描述
    data: query,					// 需要响应给客户端的数据
  })
})

// POST 接口
router.post('/post', (req, res) => {
  // 通过 req.body 获取请求体中包含的 url-encoded 格式的数据
  // 意在路由模块之前配置中间件url-encoded
  const body = req.body
  res.send({
    status: 0,
    msg: 'POST 请求成功！',
    data: body,
  })
})

// DELETE 接口
router.delete('/delete', (req, res) => {
  res.send({
    status: 0,
    msg: 'DELETE请求成功',
  })
})

module.exports = router

```



### 6.6 CORS 跨域资源共享

浏览器从一个域名的网页去请求另一个域名的资源时，域名、端口、协议任一不同，都是跨域

接口的跨域问题
刚才编写的 GET 和 POST接口，存在一个很严重的问题：不支持跨域请求，解决接口跨域问题的方案主要有两种

- CORS（主流解决方案，推荐）
- JSONP（有缺陷：只支持 GET 请求）

#### 6.6.1 使用 CORS 中间件解决跨域问题

CORS（Cross-Origin Resource Sharing，跨域资源共享）是 Express 的一个第三方中间件，由一系列 HTTP 响应头组成，这些 HTTP 响应头决定浏览器是否阻止前端 JS 代码跨域获取资源。可以很方便地解决跨域问题。使用步骤分为如下 3 步

1 运行 npm install cors 安装中间件
2 使用 const cors = require(‘cors’) 导入中间件
3 在路由之前调用 app.use(cors()) 配置中间件

注意点：

- CORS 在服务器端进行配置，客户端浏览器无须做任何额外的配置，即可请求开启了 CORS 的接口。
- CORS 在浏览器中有兼容性，只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口（如：IE10+、Chrome4+、FireFox3.5+）
  

#### 6.6.2 CORS 常见响应头

- `Access-Control-Allow-Origin`：制定了允许访问资源的外域 URL

```js
res.setHeader('Access-Control-Allow-Origin', 'http://bruceblog.io')
res.setHeader('Access-Control-Allow-Origin', '*')
```

- `Access-Control-Allow-Headers`
- 默认情况下，CORS 仅支持客户端向服务器发送如下的 9 个请求头：`Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type （值仅限于 text/plain、multipart/form-data、application/x-www-form-urlencoded 三者之一）`
- 如果客户端向服务器发送了额外的请求头信息，则需要在服务器端，通过 A`ccess-Control-Allow-Headers` 对额外的请求头进行声明，否则这次请求会失败！

```js
res.setHeader('Access-Control-Allow-Headers', 'Content-Type, X-Custom-Header')
```

- `Access-Control-Allow-Methods`
- 默认情况下，CORS 仅支持客户端发起 GET、POST、HEAD 请求。如果客户端希望通过 PUT、DELETE 等方式请求服务器的资源，则需要在服务器端，通过 `Access-Control-Alow-Methods` 来指明实际请求所允许使用的 HTTP 方法

```js
res.setHeader('Access-Control-Allow-Methods', 'POST, GET, DELETE, HEAD')
res.setHEader('Access-Control-Allow-Methods', '*')
```



#### 6.6.3 CORS 请求分类

##### 6.6.3.1 简单请求

- 请求方式：GET、POST、HEAD 三者之一
- HTTP 头部信息不超过以下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type（只有三个值 application/x-www-formurlencoded、multipart/form-data、text/plain）

##### 6.6.3.2 预检请求

- 请求方式为 GET、POST、HEAD 之外的请求 Method 类型
- 请求头中包含自定义头部字段
- 向服务器发送了 application/json 格式的数据

在浏览器与服务器正式通信之前，浏览器会先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求，所以这一次的 OPTION 请求称为“预检请求”。服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据

简单请求的特点：客户端与服务器之间只**会发生一次请求**
预检请求的特点：客户端与服务器之间会发生两次请求，**OPTION 预检请求成功之后，才会发起真正的请求**

#### 6.6.4 JSONP 接口（有缺陷只支持GET）

- 概念：浏览器端通过

**实现 JSONP 接口的步骤**：

1. 获取客户端发送过来的**回调函数的名字**
2. 得到要通过 JSONP 形式发送给客户端的数据
3. 根据前两步得到的数据，**拼接出一个函数调用的字符串**
4. 把上一步拼接得到的字符串，**响应给客户端的**

```js
const express = require('express')

const app = express()

// 配置解析表单数据的中间件
app.use(express.urlencoded({ extended: false }))

// 必须在配置 cors 中间件之前，配置 JSONP 的接口
app.get('/api/jsonp', (req, res) => {
  // 定义 JSONP 接口具体的实现过程
  const funcName = req.query.callback	// 1. 得到函数的名称
  const data = { name: 'zs', age: 22 }	// 2. 定义要发送到客户端的数据对象
  const scriptStr = `${funcName}(${JSON.stringify(data)})`	// 3. 拼接出一个函数的调用
  res.send(scriptStr)		// 4. 把拼接的字符串，响应给客户端
})

// 一定要在路由之前，配置 cors 这个中间件，从而解决接口跨域的问题
const cors = require('cors')
app.use(cors())

// 导入路由模块
const router = require('./16.apiRouter')
// 把路由模块，注册到 app 上
app.use('/api', router)

app.listen(80, () => {console.log('express server running at http://127.0.0.1')})

```

### 6.7 测试模块

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <script src="https://cdn.staticfile.org/jquery/3.4.1/jquery.min.js"></script>
  </head>
  <body>
    <button id="btnGET">GET</button>
    <button id="btnPOST">POST</button>
    <button id="btnDelete">DELETE</button>
    <button id="btnJSONP">JSONP</button>

    <script>
      $(function () {
        // 1. 测试GET接口
        $('#btnGET').on('click', function () {
          $.ajax({
            type: 'GET',
            url: 'http://127.0.0.1/api/get',
            data: { name: 'cess', age: 18 },
            success: function (res) {
              console.log(res)
            },
          })
        })
        // 2. 测试POST接口
        $('#btnPOST').on('click', function () {
          $.ajax({
            type: 'POST',
            url: 'http://127.0.0.1/api/post',
            data: { bookname: '开端', author: '祈祷君' },
            success: function (res) {
              console.log(res)
            },
          })
        })

        // 3. 为删除按钮绑定点击事件处理函数
        $('#btnDelete').on('click', function () {
          $.ajax({
            type: 'DELETE',
            url: 'http://127.0.0.1/api/delete',
            success: function (res) {
              console.log(res)
            },
          })
        })

        // 4. 为 JSONP 按钮绑定点击事件处理函数
        $('#btnJSONP').on('click', function () {
          $.ajax({
            type: 'GET',
            url: 'http://127.0.0.1/api/jsonp',
            dataType: 'jsonp',
            success: function (res) {
              console.log(res)
            },
          })
        })
      })
    </script>
  </body>
</html>


```



## 7 数据库和身份认证

### 7.1 操作Mysql

#### 配置mysql

1 安装 mysql 模块

```bash
npm install mysql
```

2 建立连接

```js
const mysql = require('mysql')

const db = mysql.createPool({
  host: '127.0.0.1',
  user: 'root',
  password: 'root',
  database: 'test',
})
```

3 测试是否正常工作

```js
db.query('select 1', (err, results) => {
  if (err) return console.log(err.message)
  console.log(results)
})
```

#### 操作 mysql 数据库

1 查询数据

```js
db.query('select * from users', (err, results) => {
  ...
})
```

2 插入数据

```js
// ? 表示占位符
const sql = 'insert into users values(?, ?)'
// 使用数组的形式为占位符指定具体的值
db.query(sql, [username, password], (err, results) => {
  if (err) return console.log(err.message)
  if (results.affectedRows === 1) console.log('插入成功')
})
```

向表中新增数据时，如果数据对象的每个属性和数据表的字段一一对应，则可以通过如下方式快速插入数据：

```js
const user = {username:'Bruce', password:'55520'}
const sql = 'insert into users set ?'
db.query(sql, user, (err, results) => {
  ...
})
```

3 更新数据

```js
const sql = 'update users set username=?, password=? where id=?'
db.query(sql, [username, password, id], (err, results) => {
  ...
})
```

快捷方式：

```js
const user = {id:7,username:'Bruce',password:'55520'}
const sql = 'update users set ? where id=?'
db.query(sql, [user, user.id], (err, results) => {
  ...
})
```

4 删除数据

```js
const sql = 'delete from users where id=?'
db.query(sql, id, (err, results) => {
  ...
})
```

使用 delete 语句会真正删除数据，保险起见，使用标记删除的形式，模拟删除的动作。即在表中设置状态字段，标记当前的数据是否被删除。

```js
db.query('update users set status=1 where id=?', 7, (err, results) => {
  ...
})
```

### 7.2 Web操作模式

#### 服务端渲染

服务器发送给客户端的 HTML 页面，是在服务器通过字符串的拼接动态生成的。因此客户端不需要使用 Ajax 额外请求页面的数据。

```js
app.get('/index.html', (req, res) => {
  const user = { name: 'Bruce', age: 29 }
  const html = `<h1>username:${user.name}, age:${user.age}</h1>`
  res.send(html)
})
```

优点：

- 前端耗时短。浏览器只需直接渲染页面，无需额外请求数据。
- 有利于 SEO。服务器响应的是完整的 HTML 页面内容，有利于爬虫爬取信息。

缺点：

- 占用服务器资源。服务器需要完成页面内容的拼接，若请求比较多，会对服务器造成一定访问压力。
- 不利于前后端分离，开发效率低

#### 前后端分离

前后端分离的开发模式，依赖于 Ajax 技术的广泛应用。后端只负责提供 API 接口，前端使用 Ajax 调用接口。

优点：

- 开发体验好。前端专业页面开发，后端专注接口开发。
- 用户体验好。页面局部刷新，无需重新请求页面。
- 减轻服务器的渲染压力。页面最终在浏览器里生成。

缺点：

- 不利于 SEO。完整的 HTML 页面在浏览器拼接完成，因此爬虫无法爬取页面的有效信息。Vue、React 等框架的 SSR（server side render）技术能解决 SEO 问题。

#### 如何选择如何选择

- **企业级网站，主要功能是展示，没有复杂交互，且需要良好的 SEO，可考虑服务端渲染**

- 后台管理项目，交互性强，无需考虑 SEO，可使用前后端分离

- 为同时兼顾首页渲染速度和前后端分离开发效率，可采用首屏服务器端渲染+其他页面前后端分离的开发模式



### 7.3 身份认证

#### Session 认证机制

服务端渲染推荐使用 Session 认证机制

原理：

![image-20230317223745230](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172237284.png)

#### Express 中使用 Session 认证

1. 安装 express-session 中间件

```bash
npm install express-session
```

2. 配置中间件

```js
const session = require('express-session')
app.use(
  session({
    secret: 'Bruce', // secret 的值为任意字符串
    resave: false,
    saveUninitalized: true,
  })
)
```

3. 向 session 中存数据

中间件配置成功后，可通过 `req.session` 访问 session 对象，存储用户信息

```js
app.post('/api/login', (req, res) => {
  req.session.user = req.body
  req.session.isLogin = true

  res.send({ status: 0, msg: 'login done' })
})
```

4. 从 session 取数据

```js
app.get('/api/username', (req, res) => {
  if (!req.session.isLogin) {
    return res.send({ status: 1, msg: 'fail' })
  }
  res.send({ status: 0, msg: 'success', username: req.session.user.username })
})
```

5. 清空 session

```js
app.post('/api/logout', (req, res) => {
  // 清空当前客户端的session信息
  req.session.destroy()
  res.send({ status: 0, msg: 'logout done' })
})
```

####  JWT 认证机制

前后端分离推荐使用 JWT（JSON Web Token）认证机制，是目前最流行的跨域认证解决方案

##### JWT 工作原理

Session 认证的局限性：

- Session 认证机制需要配合 Cookie 才能实现。由于 Cookie 默认不支持跨域访问，所以，当涉及到前端跨域请求后端接口的时候，需要做很多额外的配置，才能实现跨域 Session 认证。
- 当前端请求后端接口不存在跨域问题的时候，推荐使用 Session 身份认证机制。
- 当前端需要跨域请求后端接口的时候，不推荐使用 Session 身份认证机制，推荐使用 JWT 认证机制

JWT 工作原理图：

用户的信息通过 Token 字符串的形式，保存在客户端浏览器中。服务器通过还原 Token 字符串的形式来认证用户的身份。

 ![JWT](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303172257979.png)

JWT 组成部分：

- Header、Payload、Signature

- Payload 是真正的用户信息，加密后的字符串

- Header 和 Signature 是安全性相关部分，保证 Token 安全性

- 三者使用 `.` 分隔

  使用方式

- 客户端会把 JWT 存储在 localStorage 或 sessionStorage 中
- 此后客户端与服务端通信需要携带 JWT 进行身份认证，将 JWT 存在 HTTP 请求头 Authorization 字段中
- 加上 Bearer 前缀



##### Express 使用 JWT

1. 安装

- jsonwebtoken 用于生成 JWT 字符串
- express-jwt 用于将 JWT 字符串解析还原成 JSON 对象

```bash
npm install jsonwebtoken express-jwt
```

2. 定义 secret 密钥

- 为保证 JWT 字符串的安全性，防止其在网络传输过程中被破解，需定义用于加密和解密的 secret 密钥
- 生成 JWT 字符串时，使用密钥加密信息，得到加密好的 JWT 字符串
- 把 JWT 字符串解析还原成 JSON 对象时，使用密钥解密

```js
const jwt = require('jsonwebtoken')
const expressJWT = require('express-jwt')

// 密钥为任意字符串
const secretKey = 'Bruce'
```

3. 生成 JWT 字符串

```js
app.post('/api/login', (req, res) => {
  ...
  res.send({
    status: 200,
    message: '登录成功',
    // jwt.sign() 生成 JWT 字符串
    // 参数：用户信息对象、加密密钥、配置对象-token有效期
    // 尽量不保存敏感信息，因此只有用户名，没有密码
    token: jwt.sign({username: userInfo.username}, secretKey, {expiresIn: '10h'})
  })
})
```

4. JWT 字符串还原为 JSON 对象

- 客户端访问有权限的接口时，需通过请求头的 `Authorization` 字段，将 Token 字符串发送到服务器进行身份认证
- 服务器可以通过 express-jwt 中间件将客户端发送过来的 Token 解析还原成 JSON 对象

```js
// unless({ path: [/^\/api\//] }) 指定哪些接口无需访问权限
app.use(expressJWT({ secret: secretKey }).unless({ path: [/^\/api\//] }))
```

5. 获取用户信息

- 当 express-jwt 中间件配置成功后，即可在那些有权限的接口中，使用 `req.user` 对象，来访问从 JWT 字符串中解析出来的用户信息

```js
app.get('/admin/getinfo', (req, res) => {
  console.log(req.user)
  res.send({
    status: 200,
    message: '获取信息成功',
    data: req.user,
  })
})
```

6. 捕获解析 JWT 失败后产生的错误

- 当使用 express-jwt 解析 Token 字符串时，如果客户端发送过来的 Token 字符串过期或不合法，会产生一个解析失败的错误，影响项目的正常运行
- 通过 Express 的错误中间件，捕获这个错误并进行相关的处理

```js
app.use((err, req, res, next) => {
  if (err.name === 'UnauthorizedError') {
    return res.send({ status: 401, message: 'Invalid token' })
  }
  res.send({ status: 500, message: 'Unknown error' })
})
```



## 8 实战项目

### 8.1 项目初始化

#### 8.1.1 创建项目

新建 `api_server` 文件夹作为项目根目录，并在项目根目录中运行如下的命令，初始化包管理配置文件：

```
npm init -y
```

安装指定版本的express

```
npm i express@4.17.1
```

项目根目录中新建 `app.js` 作为整个项目的入口文件，并初始化如下的代码：

```js
const express = require('express')
// 创建 express 的服务器实例
const app = express()

// write your code here...

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(80, function () {
  console.log('api server running at http://127.0.0.1')
})
```

![image-20230320143110571](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201431626.png)

#### 8.1.2 配置 cors 跨域

运行如下的命令，安装 `cors` 中间件：

```
npm i cors@2.8.5
```

![image-20230320143053544](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201431696.png)

在 `app.js` 中导入并配置 `cors` 中间件

```js
const cors = require('cors')

app.use(cors())
```

#### 8.1.3 配置解析表单数据的中间件

通过如下的代码，配置解析 `application/x-www-form-urlencoded` 格式的表单数据的中间件：

```js
app.use(express.urlencoded({ extended: false }))
```

#### 8.1.4 初始化路由相关的文件夹

1. 在项目根目录中，新建 `router` 文件夹，用来存放所有的`路由`模块

> 路由模块中，只存放客户端的请求与处理函数之间的映射关系

1. 在项目根目录中，新建 `router_handler` 文件夹，用来存放所有的 `路由处理函数模块`

> 路由处理函数模块中，专门负责存放每个路由对应的处理函数

![image-20230320143714074](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201437131.png)

#### 8.1.5 初始化用户路由模块

在 `router` 文件夹中，新建 `user.js` 文件，作为用户的路由模块，并初始化代码：

```js
const express = require('express')
// 创建路由对象
const router = express.Router()

// 注册新用户
router.post('/reguser', (req, res) => {
  res.send('reguser OK')
})

// 登录
router.post('/login', (req, res) => {
  res.send('login OK')
})

module.exports = router
```

在 `app.js` 中，导入注册用户路由模块 ：

```js
const userRouter = require('./router/user')
app.use('/api', userRouter)
```

启动

```
nodemon app.js
```

测试：

![image-20230320150227941](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201502011.png)



#### 8.1.6 抽离用户路由模块中的处理函数

目的：为了保证 `路由模块` 的纯粹性，所有的 `路由处理函数`，必须抽离到对应的 `路由处理函数模块` 中

![image-20230320154855842](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201548922.png)

在 `/router_handler/user.js` 中，使用 `exports` 对象，分别向外共享如下两个 `路由处理函数`:

```js
/**
 * 在这里定义和用户相关的路由处理函数，供 /router/user.js 模块进行调用
 */

// 注册用户的处理函数
exports.regUser = (req, res) => {
  res.send('reguser OK')
}

// 登录的处理函数
exports.login = (req, res) => {
  res.send('login OK')
}
```

将 `/router/user.js` 中的代码修改为如下结构：

```js
const express = require('express')
const router = express.Router()

// 导入用户路由处理函数模块
const userHandler = require('../router_handler/user')

// 注册新用户
router.post('/reguser', userHandler.regUser)
// 登录
router.post('/login', userHandler.login)

module.exports = router
```

![image-20230320154809541](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201548614.png)



### 8.2 登录与注册

#### 8.2.1数据库建表

![image-20230320160040089](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303201600151.png)

#### 8.2.2 安装并配置 mysql 模块

```
npm i mysql@2.18.1
```

在项目根目录中新建 `/db/index.js` 文件，在此自定义模块中创建数据库的连接对象：

```js
const mysql = require('mysql')

// 创建数据库连接对象
const db = mysql.createPool({
  host: '127.0.0.1',
  user: 'root',
  password: 'admin123',
  database: 'my_db_01',
})

// 向外共享 db 数据库连接对象
module.exports = db
```

![image-20230321104209944](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211042116.png)

![image-20230321104241786](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211042843.png)

#### 8.2.3 注册功能实现

修改代码，测试注册功能

```js
// 注册用户的处理函数
exports.regUser = (req, res) => {
   // 获取客户端提交到服务端的用户信息
    const userinfo = req.body
    console.info(userinfo)
    res.send('reguser OK')
}
  
// 登录的处理函数
exports.login = (req, res) => {
    res.send('login OK')
}
```

![image-20230321104837313](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211048385.png)

![image-20230321104850579](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211048637.png)

增加校验

```
// 判断数据是否合法
if (!userinfo.username || !userinfo.password) {
  return res.send({ status: 1, message: '用户名或密码不能为空！' })
}
```

![image-20230321105155947](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211051004.png)

检测用户名是否被占用

```js
const db = require('../db/index')


// 注册用户的处理函数
exports.regUser = (req, res) => {

    const userinfo = req.body
    console.info(userinfo)
    //对表单数据进行校验
    // 判断数据是否合法
    if (!userinfo.username || !userinfo.password) {
        return res.send({ status: 1, message: '用户名或密码不能为空！' })
    }

    // 定义SQL语句
    const sql = `select * from ev_users where username=?`

    db.query(sql, [userinfo.username], function (err, results) {
        // 执行 SQL 语句失败
        if (err) {
            return res.send({ status: 1, message: err.message })
        }
        // 用户名被占用
        if (results.length > 0) {
            return res.send({ status: 1, message: '用户名被占用，请更换其他用户名！' })
        }
        // TODO: 用户名可用，继续后续流程...
    })
}

// 登录的处理函数
exports.login = (req, res) => {
    res.send('login OK')
}
```

![image-20230321145611555](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211456628.png)

测试功能：

![image-20230321160756554](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211607617.png)

问题记录：Cannot set headers after they are sent to the client

![image-20230321160914150](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211609208.png)

解决：

在同一接口下同时向客户端发送了两次信息。

也就是我这里使用了两次 res.send()

删掉下面多余代码即可



#### 8.2.4  对密码进行加密处理

为了保证密码的安全性，不建议在数据库以 `明文` 的形式保存用户密码，推荐对密码进行 `加密存储`

在当前项目中，使用 `bcryptjs` 对用户密码进行加密，优点：

- 加密之后的密码，**无法被逆向破解**
- 同一明文密码多次加密，得到的**加密结果各不相同**，保证了安全性

安装

```
npm i bcryptjs@2.4.3
```

在 `/router_handler/user.js` 中，导入 `bcryptjs` ：

```
const bcrypt = require('bcryptjs')
```

注册用户的处理函数中，确认用户名可用之后，调用 `bcrypt.hashSync(明文密码, 随机盐的长度)` 方法，对用户的密码进行加密处理：

```
// 对用户的密码,进行 bcrype 加密，返回值是加密之后的密码字符串
userinfo.password = bcrypt.hashSync(userinfo.password, 10)
```

![image-20230321162505676](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211625762.png)

#### 8.2.5 插入新数据

语句

```
const sql = 'insert into ev_users set ?'
```

插入用户

```js
db.query(sql, { username: userinfo.username, password: userinfo.password }, function (err, results) {
  // 执行 SQL 语句失败
  if (err) return res.send({ status: 1, message: err.message })
  // SQL 语句执行成功，但影响行数不为 1
  if (results.affectedRows !== 1) {
    return res.send({ status: 1, message: '注册用户失败，请稍后再试！' })
  }
  // 注册成功
  res.send({ status: 0, message: '注册成功！' })
})
```

![image-20230321170547805](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211705887.png)



![image-20230321170714032](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211707096.png)

![image-20230321170725999](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211707062.png)

再次插入相同的用户信息

![image-20230321171418085](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211714155.png)

#### 8.2.6 优化 res.send() 代码

在处理函数中，需要多次调用 `res.send()` 向客户端响应 `处理失败` 的结果，为了简化代码，可以手动封装一个 res.cc() 函数

在 `app.js` 中，所有路由之前，声明一个全局中间件，为 res 对象挂载一个 `res.cc()` 函数 ：

```js
// 响应数据的中间件
app.use(function (req, res, next) {
  // status = 0 为成功； status = 1 为失败； 默认将 status 的值设置为 1，方便处理失败的情况
  res.cc = function (err, status = 1) {
    res.send({
      // 状态
      status,
      // 状态描述，判断 err 是 错误对象 还是 字符串
      message: err instanceof Error ? err.message : err,
    })
  }
  next()
})
```

修改user.js

```js
const bcrypt = require('bcryptjs')

const db = require('../db/index')


// 注册用户的处理函数
exports.regUser = (req, res) => {

    const userinfo = req.body
    console.info(userinfo)
    //对表单数据进行校验
    // 判断数据是否合法
    if (!userinfo.username || !userinfo.password) {
        return res.send({ status: 1, message: '用户名或密码不能为空！' })
    }

    // 定义SQL语句
    const sql = `select * from ev_users where username=?`

    db.query(sql, [userinfo.username], function (err, results) {
        // 执行 SQL 语句失败
        if (err) {
            //return res.send({ status: 1, message: err.message })
            return res.cc(err)
        }
        // 用户名被占用
        if (results.length > 0) {
            //return res.send({ status: 1, message: '用户名被占用，请更换其他用户名！' })
            return res.cc('用户名被占用，请更换其他用户名！')
        }

        // 对用户的密码,进行 bcrype 加密，返回值是加密之后的密码字符串
        userinfo.password = bcrypt.hashSync(userinfo.password, 10)

        const sql = 'insert into ev_users set ?'

        db.query(sql, { username: userinfo.username, password: userinfo.password }, function (err, results) {
            // 执行 SQL 语句失败
           // if (err) return res.send({ status: 1, message: err.message })
           if(err) return res.cc(err)

            // SQL 语句执行成功，但影响行数不为 1
            if (results.affectedRows !== 1) {
              //return res.send({ status: 1, message: '注册用户失败，请稍后再试！' })
              return res.cc('注册用户失败，请稍后再试！')
            }
            // 注册成功
            //res.send({ status: 0, message: '注册成功！' })
            res.cc('注册成功！',0)

          })

    })
}

// 登录的处理函数
exports.login = (req, res) => {
    res.send('login OK')
}
```

![image-20230321172424985](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211724048.png)

![image-20230321172820495](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211728572.png)

测试：

![image-20230321172858444](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211728510.png)

#### 8.2.7 优化表单数据验证

表单验证的原则：前端验证为辅，后端验证为主，后端**永远不要相信**前端提交过来的**任何内容**

在实际开发中，前后端都需要对表单的数据进行合法性的验证，而且，**后端做为数据合法性验证的最后一个关口**，在拦截非法数据方面，起到了至关重要的作用。

单纯的使用 `if...else...` 的形式对数据合法性进行验证，效率低下、出错率高、维护性差。因此，推荐使用**第三方数据验证模块**，来降低出错率、提高验证的效率与可维护性，**让后端程序员把更多的精力放在核心业务逻辑的处理上**。

安装 `joi` 包，为表单中携带的每个数据项，定义验证规则：

```
npm install joi
```

安装 `@escook/express-joi` 中间件，来实现自动对表单数据进行验证的功能：

```
npm i @escook/express-joi
```

新建 `/schema/user.js` 用户信息验证规则模块，并初始化代码如下：

```js
const joi = require('joi')

/**
 * string() 值必须是字符串
 * alphanum() 值只能是包含 a-zA-Z0-9 的字符串
 * min(length) 最小长度
 * max(length) 最大长度
 * required() 值是必填项，不能为 undefined
 * pattern(正则表达式) 值必须符合正则表达式的规则
 */

// 用户名的验证规则
const username = joi.string().alphanum().min(1).max(10).required()
// 密码的验证规则
const password = joi
  .string()
  .pattern(/^[\S]{6,12}$/)
  .required()

// 注册和登录表单的验证规则对象
exports.reg_login_schema = {
  // 表示需要对 req.body 中的数据进行验证
  body: {
    username,
    password,
  },
}
```

![image-20230321173828442](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211738546.png)

修改 `/router/user.js` 中的代码如下：

```js
const express = require('express')
const router = express.Router()

// 导入用户路由处理函数模块
const userHandler = require('../router_handler/user')

// 1. 导入验证表单数据的中间件
const expressJoi = require('@escook/express-joi')
// 2. 导入需要的验证规则对象
const { reg_login_schema } = require('../schema/user')

// 注册新用户
// 3. 在注册新用户的路由中，声明局部中间件，对当前请求中携带的数据进行验证
// 3.1 数据验证通过后，会把这次请求流转给后面的路由处理函数
// 3.2 数据验证失败后，终止后续代码的执行，并抛出一个全局的 Error 错误，进入全局错误级别中间件中进行处理
router.post('/reguser', expressJoi(reg_login_schema), userHandler.regUser)
// 登录
router.post('/login', userHandler.login)

module.exports = router
```

在 `app.js` 的全局错误级别中间件中，捕获验证失败的错误，并把验证失败的结果响应给客户端：

```js
const joi = require('joi')

// 错误中间件
app.use(function (err, req, res, next) {
  // 数据验证失败
  if (err instanceof joi.ValidationError) return res.cc(err)
  // 未知错误
  res.cc(err)
})
```

![image-20230321174623506](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211746581.png)

测试：

![image-20230321174951459](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211749533.png)



#### 8.2.8 登录功能

实现步骤：

1. 检测表单数据是否合法
2. 根据用户名查询用户的数据
3. 判断用户输入的密码是否正确
4. 生成 JWT 的 Token 字符串

```
// 登录的路由
router.post('/login', expressJoi(reg_login_schema), userHandler.login)
```

根据用户名查询用户的数据

```js
// 登录的处理函数
exports.login = (req, res) => {

    const userinfo = req.body

    const sql = `select * from ev_users where username=?`

    db.query(sql, userinfo.username, function (err, results) {
        // 执行 SQL 语句失败
        if (err) return res.cc(err)
        // 执行 SQL 语句成功，但是查询到数据条数不等于 1
        if (results.length !== 1) return res.cc('登录失败！')
        // TODO：判断用户输入的登录密码是否和数据库中的密码一致
    })


}
```

![image-20230321180829280](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211808354.png)

判断用户输入的密码是否正确

> 核心实现思路：调用 `bcrypt.compareSync(用户提交的密码, 数据库中的密码)` 方法比较密码是否一致

> 返回值是布尔值（true 一致、false 不一致

```js
// 拿着用户输入的密码,和数据库中存储的密码进行对比
const compareResult = bcrypt.compareSync(userinfo.password, results[0].password)

// 如果对比的结果等于 false, 则证明用户输入的密码错误
if (!compareResult) {
  return res.cc('登录失败！')
}

// TODO：登录成功，生成 Token 字符串
```

![image-20230321181038787](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211810881.png)

![image-20230321181311347](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211813414.png)

生成 JWT 的 Token 字符串

核心注意点：在生成 Token 字符串的时候，一定要剔除 **密码** 和 **头像** 的值

```
/ 剔除完毕之后，user 中只保留了用户的 id, username, nickname, email 这四个属性的值
const user = { ...results[0], password: '', user_pic: '' }
```

首先输入

```
 const user = {... results[0]}
 console.info(user)
```

![image-20230321183719076](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211837179.png)

![image-20230321183810861](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211838960.png)

再次测试

![image-20230321183840296](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211838392.png)



如下的命令，安装生成 Token 字符串的包：

```
npm i jsonwebtoken@8.5.1
```

在 `/router_handler/user.js` 模块的头部区域，导入 `jsonwebtoken` 包：

```
// 用这个包来生成 Token 字符串
const jwt = require('jsonwebtoken')
```

![image-20230321183949338](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211839419.png)

创建 `config.js` 文件，并向外共享 **加密** 和 **还原** Token 的 `jwtSecretKey` 字符串：

```
module.exports = {
  jwtSecretKey: 'Bruce',
}
```

将用户信息对象加密成 Token 字符串

```js
// 导入配置文件
const config = require('../config')

// 生成 Token 字符串
const tokenStr = jwt.sign(user, config.jwtSecretKey, {
  expiresIn: '10h', // token 有效期为 10 个小时
})
```

![image-20230321194902694](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211949778.png)

![image-20230321194952273](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211949372.png)

优化

![image-20230321195204188](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211952271.png)

![image-20230321195821861](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211958952.png)

测试结果：

![image-20230321195929943](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303211959057.png)

将生成的 Token 字符串响应给客户端：

```js
res.send({
  status: 0,
  message: '登录成功！',
  // 为了方便客户端使用 Token，在服务器端直接拼接上 Bearer 的前缀
  token: 'Bearer ' + tokenStr,
})
```

![image-20230321200506751](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212005881.png)

![image-20230321200456874](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212004970.png)

#### 8.2.9  配置解析 Token 的中间件

运行如下的命令，安装解析 Token 的中间件

```
npm i express-jwt@5.3.3
```

在 `app.js` 中注册路由之前，配置解析 Token 的中间件：

```js
// 导入配置文件
const config = require('./config')

// 解析 token 的中间件
const expressJWT = require('express-jwt')

// 使用 .unless({ path: [/^\/api\//] }) 指定哪些接口不需要进行 Token 的身份认证
app.use(expressJWT({ secret: config.jwtSecretKey }).unless({ path: [/^\/api\//] }))
```

![image-20230321202048570](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212020662.png)

在 `app.js` 中的 `错误级别中间件` 里面，捕获并处理 Token 认证失败后的错误：

```js
// 错误中间件
app.use(function (err, req, res, next) {
  // 省略其它代码...

  // 捕获身份认证失败的错误
  if (err.name === 'UnauthorizedError') return res.cc('身份认证失败！')

  // 未知错误...
})
```

![image-20230321203026581](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212030667.png)

测试，使用http://127.0.0.1/my/testabc访问

![image-20230321203136074](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212031170.png)



### 8.3 个人中心

步骤如下：

1. 初始化 **路由** 模块
2. 初始化 **路由处理函数** 模块
3. 获取用户的基本信息

#### 8.3.1 查询个人信息

创建 `/router/userinfo.js` 路由模块，并初始化如下的代码结构：

```js
// 导入 express
const express = require('express')
// 创建路由对象
const router = express.Router()

// 获取用户的基本信息
router.get('/userinfo', (req, res) => {
  res.send('ok')
})

// 向外共享路由对象
module.exports = router
```

![image-20230321203505227](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212035325.png)

在 `app.js` 中导入并使用个人中心的路由模块：

```js
// 导入并使用用户信息路由模块
const userinfoRouter = require('./router/userinfo')
// 注意：以 /my 开头的接口，都是有权限的接口，需要进行 Token 身份认证
app.use('/my', userinfoRouter)
```

测试http://127.0.0.1/my/userinfo

![image-20230321205153217](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212051317.png)

初始化路由处理函数模块

创建 `/router/userinfo.js` 路由模块，并初始化如下的代码结构：

```js
// 获取用户基本信息的处理函数
exports.getUserInfo = (req, res) => {
  res.send('ok')
}
```

修改 `/router/userinfo.js` 中的代码如下

```js
const express = require('express')
const router = express.Router()

// 导入用户信息的处理函数模块
const userinfo_handler = require('../router_handler/userinfo')

// 获取用户的基本信息
router.get('/userinfo', userinfo_handler.getUserInfo)

module.exports = router
```

![image-20230321213341308](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212133399.png)

获取用户的基本信息

在 `/router_handler/userinfo.js` 头部导入数据库操作模块：

```
// 导入数据库操作模块
const db = require('../db/index')
```

定义SQL语句

```
const sql = `select id, username, nickname, email, user_pic from ev_users where id=?`
```

调用 `db.query()` 执行 SQL 语句：

```js
// 注意：req 对象上的 user 属性，是 Token 解析成功，express-jwt 中间件帮我们挂载上去的
db.query(sql, req.user.id, (err, results) => {
  // 1. 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 2. 执行 SQL 语句成功，但是查询到的数据条数不等于 1
  if (results.length !== 1) return res.cc('获取用户信息失败！')

  // 3. 将用户信息响应给客户端
  res.send({
    status: 0,
    message: '获取用户基本信息成功！',
    data: results[0],
  })
})
```

![image-20230321213814395](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212138497.png)

测试：

![image-20230321213747196](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212137293.png)



#### 8.3.2 更新个人信息

实现步骤：

1. 定义路由和处理函数
2. 验证表单数据
3. 实现更新用户基本信息的功能

增加路由

在 `/router/userinfo.js` 模块中，新增 `更新用户基本信息` 的路由：

```js
// 更新用户的基本信息
router.post('/userinfo', userinfo_handler.updateUserInfo)
```

在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `更新用户基本信息` 的路由处理函数：

```js
// 更新用户基本信息的处理函数
exports.updateUserInfo = (req, res) => {
  res.send('ok')
}
```

验证表单数据

在 `/schema/user.js` 验证规则模块中，定义 `id`，`nickname`，`email` 的验证规则如下：

```
// 定义 id, nickname, emial 的验证规则
const id = joi.number().integer().min(1).required()
const nickname = joi.string().required()
const email = joi.string().email().required()
```

并使用 `exports` 向外共享如下的 `验证规则对象`：

```
// 验证规则对象 - 更新用户基本信息
exports.update_userinfo_schema = {
  body: {
    id,
    nickname,
    email,
  },
}
```

![image-20230321214953174](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212149271.png)

在 `/router/userinfo.js` 模块中，导入验证数据合法性的中间件：

```
// 导入验证数据合法性的中间件
const expressJoi = require('@escook/express-joi')
```

在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```
// 导入需要的验证规则对象
const { update_userinfo_schema } = require('../schema/user')
```

在 `/router/userinfo.js` 模块中，修改 `更新用户的基本信息` 的路由如下：

```
// 更新用户的基本信息
router.post('/userinfo', expressJoi(update_userinfo_schema), userinfo_handler.updateUserInfo)
```

![image-20230321215228790](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212152899.png)

实现更新用户基本信息的功能

```js
// 更新用户基本信息的处理函数
exports.updateUserInfo = (req, res) => {
    const sql = `update ev_users set ? where id=?`

    db.query(sql, [req.body, req.body.id], (err, results) => {
        // 执行 SQL 语句失败
        if (err) return res.cc(err)
      
        // 执行 SQL 语句成功，但影响行数不为 1
        if (results.affectedRows !== 1) return res.cc('修改用户基本信息失败！')
      
        // 修改用户信息成功
        return res.cc('修改用户基本信息成功！', 0)
      })
}
```

![image-20230321215437710](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212154817.png)

测试结果：

![image-20230321215740336](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212157429.png)

![image-20230321215756683](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212157788.png)

![image-20230321215811310](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212158432.png)

#### 8.3.3 重制密码

步骤：

1. 定义路由和处理函数
2. 验证表单数据
3. 实现重置密码的功能

在 `/router/userinfo.js` 模块中，新增 `重置密码` 的路由：

```js
// 重置密码的路由
router.post('/updatepwd', userinfo_handler.updatePassword)
```

在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `重置密码` 的路由处理函数：

```js
// 重置密码的处理函数
exports.updatePassword = (req, res) => {
  res.send('ok')
}
```

验证表单数据

核心验证思路：旧密码与新密码，必须符合密码的验证规则，并且新密码不能与旧密码一致！

在 `/schema/user.js` 模块中，使用 `exports` 向外共享如下的 `验证规则对象`：

```js
// 验证规则对象 - 重置密码
exports.update_password_schema = {
  body: {
    // 使用 password 这个规则，验证 req.body.oldPwd 的值
    oldPwd: password,
    // 使用 joi.not(joi.ref('oldPwd')).concat(password) 规则，验证 req.body.newPwd 的值
    // 解读：
    // 1. joi.ref('oldPwd') 表示 newPwd 的值必须和 oldPwd 的值保持一致
    // 2. joi.not(joi.ref('oldPwd')) 表示 newPwd 的值不能等于 oldPwd 的值
    // 3. .concat() 用于合并 joi.not(joi.ref('oldPwd')) 和 password 这两条验证规则
    newPwd: joi.not(joi.ref('oldPwd')).concat(password),
  },
}
```

在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```js
// 导入需要的验证规则对象
const { update_userinfo_schema, update_password_schema } = require('../schema/user')
```

并在 `重置密码的路由` 中，使用 `update_password_schema` 规则验证表单的数据，示例代码如下

```js
router.post('/updatepwd', expressJoi(update_password_schema), userinfo_handler.updatePassword)
```

实现重置密码的功能

根据 `id` 查询用户是否存在：

```js
// 定义根据 id 查询用户数据的 SQL 语句
const sql = `select * from ev_users where id=?`

// 执行 SQL 语句查询用户是否存在
db.query(sql, req.user.id, (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 检查指定 id 的用户是否存在
  if (results.length !== 1) return res.cc('用户不存在！')

  // TODO：判断提交的旧密码是否正确
})
```

判断提交的 **旧密码** 是否正确：

```js
// 在头部区域导入 bcryptjs 后，
// 即可使用 bcrypt.compareSync(提交的密码，数据库中的密码) 方法验证密码是否正确
// compareSync() 函数的返回值为布尔值，true 表示密码正确，false 表示密码错误
const bcrypt = require('bcryptjs')

// 判断提交的旧密码是否正确
const compareResult = bcrypt.compareSync(req.body.oldPwd, results[0].password)
if (!compareResult) return res.cc('原密码错误！')
```

对新密码进行 `bcrypt` 加密之后，更新到数据库中：

```js
// 定义更新用户密码的 SQL 语句
const sql = `update ev_users set password=? where id=?`

// 对新密码进行 bcrypt 加密处理
const newPwd = bcrypt.hashSync(req.body.newPwd, 10)

// 执行 SQL 语句，根据 id 更新用户的密码
db.query(sql, [newPwd, req.user.id], (err, results) => {
  // SQL 语句执行失败
  if (err) return res.cc(err)

  // SQL 语句执行成功，但是影响行数不等于 1
  if (results.affectedRows !== 1) return res.cc('更新密码失败！')

  // 更新密码成功
  res.cc('更新密码成功！', 0)
})
```

测试结果：

![image-20230321222316978](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212223080.png)

![image-20230321222342355](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212223459.png)



#### 8.3.4更新用户头像

定义路由和处理函数

 `/router/userinfo.js` 模块中，新增 `更新用户头像` 的路由

```
// 更新用户头像的路由
router.post('/update/avatar', userinfo_handler.updateAvatar)
```

在 `/router_handler/userinfo.js` 模块中，定义并向外共享 `更新用户头像` 的路由处理函数：

```js
// 更新用户头像的处理函数
exports.updateAvatar = (req, res) => {
  res.send('ok')
}
```

在 `/schema/user.js` 验证规则模块中，定义 `avatar` 的验证规则如下：

```js
// dataUri() 指的是如下格式的字符串数据：
// data:image/png;base64,VE9PTUFOWVNFQ1JFVFM=
const avatar = joi.string().dataUri().required()
```

```js
// 验证规则对象 - 更新头像
exports.update_avatar_schema = {
  body: {
    avatar,
  },
}
```

在 `/router/userinfo.js` 模块中，导入需要的验证规则对象：

```js
const { update_avatar_schema } = require('../schema/user')
router.post('/update/avatar', expressJoi(update_avatar_schema), userinfo_handler.updateAvatar)
```

实现更新用户头像的功能

```js
const sql = 'update ev_users set user_pic=? where id=?'

db.query(sql, [req.body.avatar, req.user.id], (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 执行 SQL 语句成功，但是影响行数不等于 1
  if (results.affectedRows !== 1) return res.cc('更新头像失败！')

  // 更新用户头像成功
  return res.cc('更新头像成功！', 0)
})
```

![image-20230321223536978](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212235083.png)

测试结果

![image-20230321223509189](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212235292.png)

![image-20230321223524138](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303212235232.png)

### 8.4 文章分类列表管理

#### 8.4.1 新建数据库

![image-20230322095107571](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303220951754.png)



#### 8.4.2 查询列表

步骤

1. 初始化路由模块
2. 初始化路由处理函数模块
3. 获取文章分类列表数据

初始化路由模块

创建 `/router/.js` 路由模块，并初始化如下的代码结构：

```js
// 导入 express
const express = require('express')
// 创建路由对象
const router = express.Router()

// 获取文章分类的列表数据
router.get('/cates', (req, res) => {
  res.send('ok')
})

// 向外共享路由对象
module.exports = router
```

在 `app.js` 中导入并使用文章分类的路由模块：

```js
// 导入并使用文章分类路由模块
const artCateRouter = require('./router/artcate')
// 为文章分类的路由挂载统一的访问前缀 /my/article
app.use('/my/article', artCateRouter)
```

初始化路由处理函数模块

创建 `/router_handler/artcate.js` 路由处理函数模块，并初始化如下的代码结构

```js
// 获取文章分类列表数据的处理函数
exports.getArticleCates = (req, res) => {
  res.send('ok')
}
```

修改 `/router/artcate.js` 中的代码如下：

```js
const express = require('express')
const router = express.Router()

// 导入文章分类的路由处理函数模块
const artcate_handler = require('../router_handler/artcate')

// 获取文章分类的列表数据
router.get('/cates', artcate_handler.getArticleCates)

module.exports = router
```

获取文章分类列表数据

在 `/router_handler/artcate.js` 头部导入数据库操作模块：

```js
// 导入数据库操作模块
const db = require('../db/index')
```

SQL

```js
// 根据分类的状态，获取所有未被删除的分类列表数据
// is_delete 为 0 表示没有被 标记为删除 的数据
const sql = 'select * from ev_article_cate where is_delete=0 order by id asc'
```

调用 `db.query()` 执行 SQL 语句：

```js
db.query(sql, (err, results) => {
  // 1. 执行 SQL 语句失败
  if (err) return res.cc(err)

  // 2. 执行 SQL 语句成功
  res.send({
    status: 0,
    message: '获取文章分类列表成功！',
    data: results,
  })
})
```

![image-20230322105020911](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221050004.png)

#### 8.4.3 根据ID删除

步骤：

1. 定义路由和处理函数
2. 验证表单数据
3. 实现删除文章分类的功能

路由和处理函数

在 `/router/artcate.js` 模块中，添加 `删除文章分类` 的路由：

```js
// 删除文章分类的路由
router.get('/deletecate/:id', artcate_handler.deleteCateById)
```

在 `/router_handler/artcate.js` 模块中，定义并向外共享 `删除文章分类` 的路由处理函数：

```js
// 删除文章分类的处理函数
exports.deleteCateById = (req, res) => {
  res.send('ok')
}
```

验证表单数据

在 `/schema/artcate.js` 验证规则模块中，定义 id 的验证规则如下：

```js
// 定义 分类Id 的校验规则
const id = joi.number().integer().min(1).required()

// 校验规则对象 - 删除分类
exports.delete_cate_schema = {
  params: {
    id,
  },
}
```

在 `/router/artcate.js` 模块中，导入需要的验证规则对象，并在路由中使用：

```js
//1 导入验证数据的中间件
const expressJoi = require('@escook/express-joi')
// 导入删除分类的验证规则对象
const { delete_cate_schema } = require('../schema/artcate')

// 删除文章分类的路由
router.get('/deletecate/:id', expressJoi(delete_cate_schema), artcate_handler.deleteCateById)
```

实现删除文章分类的功能

```
const sql = `update ev_article_cate set is_delete=1 where id=?`
```

```js
db.query(sql, req.params.id, (err, results) => {
  // 执行 SQL 语句失败
  if (err) return res.cc(err)

  // SQL 语句执行成功，但是影响行数不等于 1
  if (results.affectedRows !== 1) return res.cc('删除文章分类失败！')

  // 删除文章分类成功
  res.cc('删除文章分类成功！', 0)
})
```

测试http://127.0.0.1/my/article/deletecate/2

![image-20230322111323890](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221113973.png)



### 8.5 生成接口文档

官方网址：https://apidocjs.com/

![image-20230322140835561](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221408667.png)

#### 8.5.1 全局安装

```
npm install apidoc -g
```

#### 8.5.2 修改 package.json文件

```json
  "apidoc": {  
    "title": "接口文档", 
    "url": "http://localhost:3000" 
  }
```

![image-20230322140000000](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221400081.png)

添加apidoc.json

```json
{
    "name": "企业管理系统接口文档",
    "version": "1.0.0",
    "description": "关于CMS系统的接口",
    "title": "企业管理系统接口文档",
    "url" : "http://127.0.0.1"
  }
```

![image-20230322140632721](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221406823.png)

#### 8.5.3 给接口添加注释

```js
/**
* @api {post} /api/reguser 用户注册
* @apiDescription 用户注册
* @apiGroup 用户模块
* @apiParam {String} username  用户名 
* @apiParam {String} password 密码 
* @apiVersion 1.0.0
*/
router.post('/reguser', expressJoi(reg_login_schema),userHandler.regUser)

/**
* @api {post} /api/User/login 用户登录
* @apiDescription 用户登录
* @apiGroup 用户模块
* @apiParam {String} username  用户名 
* @apiParam {String} password 密码 
* @apiVersion 1.0.0
*/
router.post('/login',  expressJoi(reg_login_schema), userHandler.login)
```

![image-20230322140201618](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221402698.png)



#### 8.5.4 生成apidoc

```
apidoc -i router/ -o public/apidoc/
```

测试效果：

![image-20230322140459341](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303221404447.png)
