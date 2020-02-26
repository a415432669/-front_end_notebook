### 一、关于session

session是另一种记录客户状态的机制，与cookie保存在客户端浏览器不同，session保存在服务器当中；
当客户端访问服务器时，服务器会生成一个session对象，对象中保存的是key:value值，同时服务器会将key传回给客户端的cookie当中；当用户第二次访问服务器时，就会把cookie当中的key传回到服务器中，最后服务器会吧value值返回给客户端。
因此上面的key则是全局唯一的标识，客户端和服务端依靠这个全局唯一的标识来访问会话信息数据。

### 二、设置session

我们使用express-session模块来设置session

##### 1.安装express-session

```
cnpm install express-session --save
```

##### 2.引入express-session模块

```
const session=require("express-session");
```

##### 3.设置session

```
session(options);
```


如下列代码：

```
const express=require("express");
const session=require("express-session");

var app=express();

//配置中间件
app.use(session({
	secret: "keyboard cat",
	 resave: false,
	 saveUninitialized: true,
	 cookie: ('name', 'value',{maxAge:  5*60*1000,secure: false})
}));

app.use('/login',function(req,res){
	//设置session
	req.session.userinfo='张三';
	res.send("登陆成功！");
});

app.use('/',function(req,res){
	//获取session
	if(req.session.userinfo){
		res.send("hello "+req.session.userinfo+"，welcome");
	}else{
		res.send("未登陆");
	}
});

app.listen(8080);
```

在session(option)中对session进行设置

### 三、session的常用方法

```
//设置session
req.session.username="张三"

//获取session
req.session.username

//重新设置cookie的过期时间
req.session.cookie.maxAge=1000;

//销毁session
req.session.destroy(function(err){
	
})
```


以下演示通过销毁session的方式来退出登录

```
const express=require("express");
const session=require("express-session");

var app=express();

//配置中间件
app.use(session({
	secret: "keyboard cat",
	 resave: false,
	 saveUninitialized: true,
	 cookie: ('name', 'value',{	maxAge:  5*60*1000,
								secure: false,
								name: "seName",
								resave: false})
}));

app.use('/login',function(req,res){
	//设置session
	req.session.userinfo='张三';
	res.send("登陆成功！");
});

app.use('/loginOut',function(req,res){
	//注销session
	req.session.destroy(function(err){
		res.send("退出登录！"+err);
	});
});

app.use('/',function(req,res){
	//获取session
	if(req.session.userinfo){
		res.send("hello "+req.session.userinfo+"，welcome to index");
	}else{
		res.send("未登陆");
	}
});

app.listen(8080);
```

当我们进入到主页时，未显示任何信息，进入login路由后，自动设置session，这是回到主页则显示session信息，之后进入loginOut路由已注销session信息，再回到首页显示为登陆。

