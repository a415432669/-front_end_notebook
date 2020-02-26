### 一、关于POST请求

post方法作为http请求很重要的一部分，几乎所有的网站都有用到它，与get不同，post请求更像是在服务器上做修改操作，它一般用于数据资源的更新。
相比于get请求，post所请求的数据会更加安全。上一章中我们发现get请求会在地址栏显示输入的用户名和密码(有中文时会转化为BASE64加密)，而post请求则会将数据放入http包的包体中，这使得别人无法直接看到用户名和密码！

### 二、Express如何设置POST请求

1.我们的知道，首先我们得知道在form表单进行post请求，enctype属性一般设置为“application/x-www-form-urlencoded”，如果设置成multipart/form-data，则多用于文件上传，如下：

```html
<form action="#" method="post" enctype="application/x-www-form-urlencoded">
</form>
```

2设置解析body中间件

```
app.use(express.urlencoded())
```

3获取body数据

```
req.body.username 
```



登陆案例：

HTML:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>登陆</h1>
    <form action="/login" method="POST">
        <div>
            用户名：<input type="text" name="username">
        </div>
        <div>
            密码：<input type="password" name="password">
        </div>
        <button>登陆</button>
    </form>
      
</body>
</html>
```

APP.JS

```
var express = require('express');
var path = require('path')
var app = express();
var sqlQuery = require('./lcMysql')

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
app.use(express.static(path.join(__dirname, 'public')));
//解析post提交的数据
app.use(express.urlencoded())

//搜索首页
app.get('/',(req,res)=>{
  res.render('index.ejs')
})

//登陆页
app.get('/login',(req,res)=>{
  res.render('login')
})
//处理登陆请求
app.post('/login',async (req,res)=>{
  //获取用户名和密码
  let username = req.body.username 
  let password = req.body.password
  //查询数据库是否由此用户名和密码
  let sqlStr = 'select * from user where username = ? and password = ?';
  let arr = [username,password];
  let result = await sqlQuery(sqlStr,arr)
  if(result.length == 0 ){
    res.send("登陆失败")
  }else{
    res.send("登陆成功")
  }

})



module.exports = app;

```

