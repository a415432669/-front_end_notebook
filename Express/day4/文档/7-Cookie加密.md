### 一、关于cookie加密

cookie加密是让客户端用户无法的获取cookie明文信息，是数据安全的重要部分；一般的我们可以在保存cookie时对cookie信息进行加密，或者在res.cookie中对option对象的signed属性设置设置成true即可。

### 二、使用 signed 属性进行cookie加密

如下列代码：

```
const express = require("express");
const cookieParser = require("cookie-parser");

var app = express();
app.use(cookieParser('secret'));

app.get("/",function(req,res){
	res.send("主页");
});

//获取cookie
app.use(function(req,res,next){
	console.log(req.signedCookies.name);
	next();
});

//设置cookie
app.use(function(req,res,next){
	console.log(res.cookie("name","zhangsan",{httpOnly: true,maxAge: 200000,signed: true}));
	res.end("cookie为："+req.signedCookies.name);
});

app.listen(8080);
```

**签名原理**
Express用于对cookie签名，而cookie-parser则是实现对签名的解析。实质是把cookie设置的值和cookieParser(‘secret’);中的secret进行hmac加密，之后和cookie值加“.”的方式拼接起来。
当option中signed设置为true后，底层会将cookie的值与“secret”进行hmac加密；

**如何解析**
cookie-parser中间件在解析签名cookie时做了两件事：

1. 将签名cookie对应的原始值提取出来
2. 验证签名cookie是否合法

### 3、直接对cookie值加密

node为我们提供了一个核心安全模块“crypto”，它提供了很多安全相关的功能，如摘要运算、加密、电子签名等。
这是，我们便可很轻易的封装一个加密模块：

```
const crypto=require('crypto');

module.exports={
	//MD5封装
	MD5_SUFFIX:'s5w84&&d4d473885s2025s5*4s2',
	md5:function(str){
		var obj=crypto.createHash('md5');
		obj.update(str);		
		return obj.digest('hex');
	}
}
```


之后只需要进行相应导入即可

```
const common=require('./MD5');

var str='123456';
var str=common.md5(str+'s5w84&&d4d473885s2025s5*4s2');
console.log(str);
```


设置cookie代码如下：

```
const express=require("express");
const cookieParser=require("cookie-parser");
var cry = require('./md5');

var app=express();

var str='hello-123';
var str=cry.md5(str+'s5w84&&d4d473885s2025s5*4s2');

//设置中间件
app.use(cookieParser());

//获取加密cookie
app.use(function(req,res,next){
	console.log(req.cookies.userName);
	next();
});

//设置并加密cookie
app.use(function(req,res,next){
	res.cookie("userName", str, {maxAge: 5*60*1000, httpOnly: true});
	res.end("set ok");
});

app.listen(8080);
```

如果是在判断登录时，只需将用户输入的账号进行同样加密操作在进行比较即可知道账户是否正确。
crypto所涉及的加密方式有很多，推荐大家都写模块引用，这样更方便后期的维护。

