# 中间件

从字面意思，我们可以了解到它大概就是做中间代理操作，事实也是如此；大多数情况下，中间件就是在做接收到请求和发送响应中间的一系列操作。事实上，express是一个路由和中间件的web框架，Express 应用程序基本上是一系列中间件函数的调用。



1.浏览器发送请求

2.express接受请求

中间处理的过程

3.路由函数处理渲染（req,res）

4.res.render渲染



中间件函数可以执行以下任务：

* 执行任何代码。
* 对请求和响应对象进行更改。
* 结束请求/响应循环。
* 调用堆栈中的下一个中间件函数。

中间件也分为应用层中间件、路由中间件、内置中间件、错误处理中间件和第三方中间件。下面分别对以下进行说明：

### 1.应用层中间件

应用级中间键绑定到app对象使用app.use和app.METHOD()-需要处理http请求的方法，例如GET、PUT、POST，将之前的get或者post替换为use就行。
例如下面实例：

```
const express=require("express");

var app=express();

//匹配路由之前的操作
app.use(function(req,res,next()){
	console.log("访问之前");
});

app.get("/",function(req,res){
	res.send("主页");
});

app.listen(8080);
```


这时我们会发现http://localhost:8080/地址一直在加载，但命令行里显示了“访问之前”，说明程序并不会同步执行，如果使用next来是路由继续向下匹配，那么就能又得到主页数据了：

```
const express=require("express");

var app=express();

//匹配路由之前的操作
app.use(function(req,res,next){
	console.log("访问之前");
	next();
});

app.get("/",function(req,res){
	res.send("主页");
});

app.listen(8080);
```


当然也可以简化写法：

```
const express=require("express");

var app=express();

app.use(function(req,res,next){
	console.log("访问之前");
	next();
},function(req,res){
	res.send("主页");
});

app.listen(8080);
```

因此，在进行路由匹配之前或再录又要继续向下执行时想做个操作，那么应用层中间件无疑是好的选择。

### 2.路由中间件

路由级中间件和应用级中间件类似，只不过他需要绑定express.Router();



```
var router = express.Router()
```


在匹配路由时，我们使用 router.use() 或 router.VERB() ,路由中间件结合多次callback可用于用户登录及用户状态检测。

```
const express = require("express");
var app = express();
var router=express.Router();

router.use("/",function(req,res,next){
	console.log("匹配前");
	next();
});

router.use("/user",function(req,res,next){
	console.log("匹配地址：",req.originalUrl);
	next();
},function(req,res){
	res.send("用户登录");
});

app.use("/",router);

app.listen(8080);
```


总之在检测用户登录和引导用户应该访问哪个页面是，路由中间件绝对好用。

### 3.错误处理中间件

顾名思义，它是指当我们匹配不到路由时所执行的操作。错误处理中间件和其他中间件基本一样，只不过其需要开发者提供4个自变量参数。

```
app.use((err, req, res, next) => {
        res.sendStatus(err.httpStatusCode).json(err);
});
```


一般情况下，我们把错误处理放在最下面，这样我们即可对错误进行集中处理。

```
const express=require("express");

var app=express();

app.get("/",function(req,res,next){
	const err=new Error('Not Found');
	res.send("主页");
	next(err);
});

app.use("/user",function(err,req,res,next){
	console.log("用户登录");
	next(err);
},function(req,res,next){
	res.send("用户登录");
	next();
});

app.use(function(req,res){
	res.status(404).send("未找到指定页面");
});

app.listen(8080);
```



### 4.内置中间件

从版本4.x开始，Express不再依赖Content，也就是说Express以前的内置中间件作为单独模块，express.static是Express的唯一内置中间件。

```
express.static(root, [options]);
```

通过express.static我们可以指定要加载的静态资源。

### 5.第三方中间件

形如之前我们的body-parser，采用引入外部模块的方式来获得更多的应用操作。如后期的cookie和session。

```
var express = require('express');
var app = express();
var cookieParser = require('cookie-parser');
```


以上就是关于express中间件类型，在实际项目中，中间件都是必不可少的，因此熟悉使用各种中间件会加快项目的开发效率。

