### 一、关于get请求

一般在网站开发中，get都用作数据获取和查询，类似于数据库中的查询操作，当服务器解析前台资源后即传输相应内容；而查询字符串是在URL上进行的，形如：

```
http://localhost:8080/login?goods1=0001&goods2=0002
```



### 二、获取前台get请求

通过req.query可以获得用户发送的get请求，之后通过node操作将相应数据返回给用户。

如果发送的是：

```
http://localhost:8080/login?goods1=0001&goods2=0002
```


响应的话则通过：

```
req.query
```


他会获取到全部数据，或

```
req.query.goods1
req.query.goods2
```


来单独或去每一个数据。总之不同的需求对应不同的业务，大家按自己的需要来获取；

### 三、实例

下面通过一个实例来对获取get参数进行一个总结：

HTML:

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<form action="http://localhost:8080/login" method="get">
			用户：
			<input type="text" name="user" id="user" placeholder="用户名"/>
			<br>
			密码：
			<input type="password" name="password" id="password" placeholder="密码"/>
			<br>
			<input type="submit" value="提交"/>
		</form>
	</body>
</html>
```

NODE:

```
const express = require("express");
var app = express();

app.get("/",function(req,res){
	res.send("主页");
});

app.get("/login",function(req,res){
	console.log(req.query);
	res.send("登录路由，user为："+req.query.user+"==>   password为："+req.query.password);
});

app.listen(8080);
```



当在html页面中输入用户和密码提交后：

就能得到想要的传输数据；
总之，虽然获取get参数并不复杂，但使用频率却相当的高，对于任何技术我们都应该秉持认真的态度去了解和学习它。
