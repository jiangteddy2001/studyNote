# Nodejs

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

```
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



### 2.3 写入文件

fs.writeFile(path, data[, option], callback) 向指定的文件中写入内容
path 必选参数，字符串，文件路径
data 必选参数，写入的内容
option 可选参数，设置字符集，默认值是 utf8
callback 必选参数，文件写入完成后的回调函数
注意：写入会覆盖原内容

















