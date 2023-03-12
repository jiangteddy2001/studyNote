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

- 由于 module.exports 单词写起来比较复杂，为了简化向外共享成员的代码，Node 提供了 exports 对象。默认情况下，exports 和 module.exports 指向同一个对象。最终共享的结果，还是以 module.exports 指向的对象为准。
- 时刻谨记，require() 模块时，得到的永远是 module.exports 指向的对象，若出现exports 和 module.exports，最终不管exports怎么指向，都输出module.exports。注意挂载属性和指向新对象的区别。
  

![image-20230312150717035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312150717035.png)

```
console.log(exports)

console.log(module.exports)

console.log(exports== module.exports)
```

![image-20230312151350936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230312151350936.png)

注意：为了防止混乱，建议大家不要在同一个模块中同时使用 exports 和 module.exports