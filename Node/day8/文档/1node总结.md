Node：一门后端语言（服务器端的程序语言），能够连接数据库存取数据，能够接受和处理网络请求（服务器的响应，发送请求去获取数据），单线程事件驱动，异步执行，不等待，提高IO（input和ouput）的处理速度和效率。

服务器：本质上是一台PC主机（linux系统，windows系统），部署了后端语言的执行环境，并且能够长时间提供网络服务。

### 事件驱动

node本身提供了事件对象，帮助我们快速订阅者模式，或者观察者模式，或者事件模式。

```
//事件的订阅
event.on(‘林俊杰演唱会’，()=>{订阅门票})
//事件的触发
event.emit(‘林俊杰演唱会’)
```

### 读写事件

```
fs.readfile('path',读取配置,(err,data)=>{})
fs.writeFile('path',写入数据，写入配置，()=>{})
```

### 读写的promise封装

```javascript
let fs = require('fs')
function fsRead(path){
    return new Promise(function(resolve,reject){
        fs.readFile(path,{flag:'r',encoding:"utf-8"},function(err,data){
            if(err){
                //console.log(err)
                //失败执行的内容
                reject(err)

            }else{
                //console.log(data)
                //成功执行的内容
                resolve(data)
            }
            //console.log(456)
        })
    })
}


function fsWrite(path,content){
    return new Promise(function(resolve,reject){
        fs.writeFile(path,content,{flag:"a",encoding:"utf-8"},function(err){
            if(err){
                //console.log("写入内容出错")
                reject(err)
            }else{
                resolve(err)
                //console.log("写入内容成功")
            }
        })
    })
}

function fsDir(path){
    return new Promise(function(resolve,reject){
        fs.mkdir(path,function(err){
            if(err){
                reject(err)
            }else{
                resolve("成功创建目录")
            }
        })
    })
}

module.exports = {fsRead,fsWrite,fsDir}
```

#### 使用方式

```
(async function(){
	let data = await fsRead('path')
})()
```

### 网络请求数据

request,axios:效率比较高，单局限性比较大

puppeteer:效率低，局限性比较小

重点掌握的是：页面的分析，数据存放的位置，以及响应内容。

### 网络响应数据

http.createServer：就可以创建1个服务器去监听某个端口，并且通过请求事件来处理每个发送过来的请求。

server.on('request',(req,res)=>{

​	req:请求数据都会放在请求对象里

​	res：能够做出响应对象

})	

### 路由

根据不同的路径去响应不同的内容

```javascript
//循环匹配正则路径
for(let key in this.reqEvent){
    res.setHeader("content-type","text/html;charset=utf-8")
    let regStr = key
    let reg = new RegExp(regStr,'igs');
    //console.log(regStr,reg)
    if(reg.test(req.url)){
        this.reqEvent[key](req,res)
        resState = true
        break;
    }
}
```

### 模板

会有个固定样式和结构的HTML模板，根据请求的数据不同，显示页面内容。例如新闻网站

```javascript
function render(options,path){
    fs.readFile(path,{encoding:"utf-8",flag:"r"},(err,data)=>{
        if(err){
            console.log(err)
        }else{
            try {
                data = replaceArr(data,options)
                data = replaceVar(data,options)
            } catch (error) {
               console.log(error)     
            }

            this.end(data)
        }
    })
}
```









