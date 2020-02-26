### 一、简介

相比于jade模板引擎，ejs对原HTML语言就未作出结构上的改变，只不过在其交互数据方面做出了些许修改，相比于jade更加简单易用。因此其学习成本是很低的。您也可参考ejs官网：https://ejs.bootcss.com/

### 二、ejs基本使用

这里我们使用如下配置文件：

我们啊可以通过下面的方式实现基本的ejs操作：
app.js文件：

```
const express=require("express");
const ejs=require("ejs");
const fs=require("fs");

var app=express();

//引用ejs
app.set('views',"public");	//设置视图的对应目录
app.set("view engine","ejs");		//设置默认的模板引擎
app.engine('ejs', ejs.__express);		//定义模板引擎

app.get("/",function(req,res){
	res.render("index.ejs",{title: "<h4>express</h4>"});
});

app.listen(8080);
```


ejs文件：

```HTML
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<% for(var i=0;i<10;i++){ %>
			<%= i %>
		<% } %>
		<!-- 获取变量 -->
		<div class="datas">
			<p>获取变量：</p>
			<%- title %>
			<%= title %>
		</div>
	</body>
</html>
```

这时我们会得到如下图的结果：

由此可以知道：

```
<% xxx %>：里面写入的是js语法，
<%= xxx %>：里面是服务端发送给ejs模板转义后的变量，输出为原html
<%- xxx %>：里面也是服务端发送给ejs模板后的变量，不过他会把html输出来
<%# 注释标签，不执行、不输出内容
```

同理res.render()函数也是支持回调的：

```
res.render('user', { name: 'Tobi' }, function(err, html) {
  console.log(html);
});
```


这样我们即可将看到heml的内容。另外值得说明的是ejs模块也有ejs.render()和ejs.renderFile()方法，他在这里与res.render()作用类似，如下：

```
ejs.render(str, data, options);

ejs.renderFile(filename, data, options, function(err, str){
    // str => 输出绘制后的 HTML
});
```

### 三、ejs标签各种含义

```
<% '脚本' 标签，用于流程控制，无输出。
<%_ 删除其前面的空格符
<%= 输出数据到模板（输出是转义 HTML 标签）
<%- 输出非转义的数据到模板
<%# 注释标签，不执行、不输出内容
<%% 输出字符串 '<%'
%> 一般结束标签
-%> 删除紧随其后的换行符
_%> 将结束标签后面的空格符删除
```


以上就为ejs基本用法，往后对数据库操作就直接把json数据从服务器返送给模板引擎就行；

