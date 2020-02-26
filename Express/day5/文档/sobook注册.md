# sobook注册

### 1-注册页

1-设置表单

```HTML
<form action="/register" method="POST">
    <div class="form-group">
        <input class="form-control" type="email" name="mail" placeholder="邮箱" required="required"/>
    </div>
    <div class="form-group">
        <input class="form-control" type="text" name="username" placeholder="用户名" required="required"/>
    </div>
    <div class="form-group">
        <input class="form-control" type="password" name="password" placeholder="密码" required="required"/>
    </div>
    <div class="form-group">
        <input class="form-control" type="password" name="repassword" placeholder="再次输入密码" required="required"/>
    </div>

    <p><a href="/login">登陆</a></p>
    <button id="registerBtn" disabled="disable" style="cursor: not-allowed;" class="btn btn-lg">注册</button>
</form>
```

2-前端校验表单数据

```javascript
var formDiv = document.querySelector('form');
	var inputs =  document.querySelectorAll('form input');
	var btn = document.querySelector('#registerBtn');
	formDiv.oninput = function(){
		//判断是否有内容为空
		isAble = true;
		inputs.forEach((item,i)=>{
			if(item.value==""){
				isAble = false;
			}
		})
		//正则匹配邮箱地址
		let reg = /^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/
		//密码与再次输入的密码是否匹配
		if(inputs[2].value==inputs[3].value&&isAble&&reg.test(inputs[0].value)){
			btn.disabled = false;
			btn.style.cursor = 'pointer'
		}else{
			btn.disabled = true;
			btn.style.cursor = 'not-allow'
		}
	}
```



### 2-注册页的路由

1-正常浏览器GET请求的路由

```
router.get('/', function(req, res, next) {
  res.render('register')
});
```

2-表单提交的POST请求路由，先判断是否已注册，没有注册即将数据插入到数据库

```javascript
router.post('/',async function(req,res){
    //获取表单提交的邮箱，密码，用户名
    console.log(req.body)
    let mail = req.body.mail;
    let password = jiami(req.body.password);
    let username = req.body.username;
    //判断邮箱是否已注册，如果已注册，将不在注册；
    let strSql = "select * from user where mail=?"
    let result = await sqlQuery(strSql,[mail])
    if(result.length!=0){
        //邮箱已注册
        res.render('info',{
            title:"注册失败",
            content:"此邮箱已注册过，可直接登陆，或找寻密码",
            href:"/register",
            hrefTxt:"注册页"
        })
        
    }else{
       //此邮箱尚未注册，可注册
       strSql = "insert into user (mail,username,password) values (?,?,?)"
       await sqlQuery(strSql,[mail,username,password])
       res.render('info',{
        title:"注册成功",
        content:"注册成功请登陆，即将进入登陆页面",
        href:"/login",
        hrefTxt:"登录页"
    })
    }
})
```



### 3-加密密码并保存至数据库

加密：

```
function jiami(str){
    let salt = "fjdsoigijasoigjasdiodgjasdiogjoasid"
    let obj = crypto.createHash('md5')
    str = salt+str;
    obj.update(str)
    return obj.digest('hex')
}
```

### 4-修改登陆也为加密操作

```javascript
function jiami(str){
    let salt = "fjdsoigijasoigjasdiodgjasdiogjoasid"
    let obj = crypto.createHash('md5')
    str = salt+str;
    obj.update(str)
    return obj.digest('hex')
}

router.post('/',async function(req,res){
    console.log(req.body)
    //根据提交的邮箱和密码判断是否是正确的账号密码
    let strSql = "select * from user where mail=? and password = ?"
    let arr = [req.body.mail,jiami(req.body.password)]
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

