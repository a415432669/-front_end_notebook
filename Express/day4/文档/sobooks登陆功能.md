# sobooks登陆功能

### 1-引入session和cookie相关模块

```
var cookieParser = require('cookie-parser');
//引入session模块
let session = require('express-session');
```

### 2-引入session

```
app.use(session({
  secret: "xzsagjasoigjasoi",
  resave:true,//强制保存session
  cookie:{
    maxAge:7*24*60*60*1000,//设置session的有效期为1周
  },
  saveUninitialized:true//是否保存初始化的session
}))
```

### 3-引入cookie中间件

```
app.use(cookieParser('secret'));
```

### 4-写判断是否登陆的中间件

```
function isLoginMid(req,res,next){
    if(req.session.username==undefined){
        res.render('info',{
            title:"未登录",
            content:"尚未登陆，请进入登陆页面登陆",
            href:"/login",
            hrefTxt:"登录页"
        })
    }else{
        //一登录进入正常页面
        next()
    }
}
```

### 5-引入一个跳转的模板信息页面

可以显示登陆成功或者失败的信息内容，并且可以在一定时间内进行跳转。

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title><%-title%></title>
</head>
<body>
    <h1><%-title%></h1>
    <h3><%-content%></h3>
    <p><span class="num">5</span>秒后跳转至：<a href="<%-href%>"><%-hrefTxt%></a></p>
    <script>
        n = 5;
        setInterval(()=>{
            n--;
            if(n<0){
                location.href = '<%-href%>';
            }else{
                document.querySelector('.num').innerHTML = n;
            }
        },1000)
    </script>
</body>
</html>
```

### 6-登陆页面

```javascript
<form action="/login" method="POST">
    <div class="form-group">
        <input class="form-control" type="email" name="mail" placeholder="邮箱" required="required"/>
            </div>
<div class="form-group">
    <input class="form-control" type="password" name="password" placeholder="密码" required="required"/>
        </div>
<p><a href="#">忘记密码?</a></p>
    <button class="btn btn-lg">登录</button>
</form>
```

### 7-处理POST方式提交的请求

1-获取表单提交的数据

2-查询表单提交的账号密码是否正确

3-如果正确，设置session,req.session.username = user.username;

4-显示登陆是否成功信息

```
router.post('/',async function(req,res){
    console.log(req.body)
    //根据提交的邮箱和密码判断是否是正确的账号密码
    let strSql = "select * from user where mail=? and password = ?"
    let arr = [req.body.mail,req.body.password]
    let result = await sqlQuery(strSql,arr)
    if(result.length!=0){
        //登陆成功
        user = result[0];
        req.session.username = user.username;
        res.render('info',{
            title:"登陆成功",
            content:"账号密码正确，即将进入首页",
            href:"/",
            hrefTxt:"首页"
        })
    }else{
        res.render('info',{
            title:"登陆失败",
            content:"账号或密码不正确，即将进入登录页",
            href:"/login",
            hrefTxt:"登录页"
        })
    }
})
```

