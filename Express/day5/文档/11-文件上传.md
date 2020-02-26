### 一、multer中间件

再上传文件时，我们通常会使用到他。Multer用于处理multipart/form-data 类型的表单数据。首先我们先安装它：

```
cnpm install multer --save
```

### 二、使用

首先在form表单中我们需要设置enctype为：multipart/form-data表单类型。同时我们也需要用到fs模块对文件重命名。下面是单文件上传实例：

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<form action="http://localhost:8080/" method="post" enctype="multipart/form-data">
			<input type="file" name="files" value="指定文件">
			<br><br>
			<input type="submit" value="上传">
		</form>
	</body>
</html>
```

NODE代码：

```
const express=require("express");
const multer=require('multer');
//初始化上传对象
var upload=multer({dest:'./upload/'});
var fs = require('fs');


var app=express();

app.use("/",upload.single("files"),function(req,res){	//files为input type="file"的name值
	var oldFile=req.file.destination+req.file.filename;	//指定旧文件
	var newFile=req.file.destination + req.file.originalname;	//指定新文件
	fs.rename(oldFile,newFile,function(err){
		if(err){
			res.send('上传失败！');
		}else{
			res.send('上传成功！');
		}
	});
});

app.listen(8080);
```


在这里我们可以指定单个文件上传到根目录的upload文件夹里。这里值得注意的是req.file会返回文件的基本信息：

```
  fieldname: ***,	//input type="file"的name值
  originalname: ***,	//用户计算机上的文件的名称
  encoding: '***',		//文件编码
  mimetype: ***',		//文件的 MIME 类型
  destination: './***/',		//保存路径
  filename: ***,		//保存在 destination 中的文件名
  path: ***,		//已上传文件的完整路径
  size: **		//文件大小（字节单位）
```



### 三、上传多个文件

在HTML找中input type="file"需要加上multiple来实现过滤，multiple不写参数则可以读取·所有文件。而在服务端上，我们需要将single()改为array(“name”,num);的形式来接收多个文件的上传请求。最后对他们全部进行重命名。在这之前我们首先看看multer支持哪些文件上传方式：

```
.single(fieldname)	//接受一个以 fieldname 命名的文件。.fields(fields)
.array(fieldname[, maxCount])	//接受一个以 fieldname 命名的文件数组。可以配置 maxCount 来限制上传的最大数量。
.fields(fields)	//接受指定 fields 的混合文件。fields是一个拥有name和maxCount的数组对象。
.none()		//只接受文本域。如果任何文件上传到这个模式，将发生 "LIMIT_UNEXPECTED_FILE" 错误。
.any()	//接受一切上传的文件。
```

下面我们将会演示如何上传多个文件：
html：

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<form action="http://localhost:8080/" method="post" enctype="multipart/form-data">
			<input type="file" name="files" value="指定文件" multiple>
			<br><br>
			<input type="submit" value="上传">
		</form>
	</body>
</html>
```

node代码：

```
const express=require("express");
const multer=require('multer');
var upload=multer({dest:'./upload/'});
var fs = require('fs');


var app=express();

app.use("/",upload.array("files",5),function(req,res,next){
	req.files.forEach(function(ele,index){
		console.log(ele);
		var oldFile=ele.destination+ele.filename;	//指定旧文件
		var newFile=ele.destination+ele.originalname;	//指定新文件
		fs.rename(oldFile,newFile,function(err){
			err?console.log('上传失败！'):console.log('上传成功！');
		});
	});
	res.send("成功上传");
});

app.listen(8080);
```


这里，我们获取文件信息是通过req.files来获取，他是由数组构成的对象，之后用foreach循环对其进行重命名即可。

### 四、通过limits来限制上传文件

Multer通过使用limits这个对象来对数据进行限制，它允许使用以下参数：

```
Key	Description	Default
fieldNameSize	field 名字最大长度	100 bytes
fieldSize	field 值的最大长度	1MB
fields	非文件 field 的最大数量	无限
fileSize	在 multipart 表单中，文件最大长度 (字节单位)	无限
files	在 multipart 表单中，文件最大数量	无限
parts	在 multipart 表单中，part 传输的最大数量(fields + files)	无限
headerPairs	在 multipart 表单中，键值对最大组数	2000
如果你上传的文件超出这些设定，MulterError模块将会启用，该模块在node_modules/multer/lib/multer-error.js上：
```

我们可以使用err.code定位到该错误，他有7种code方式，不同设置会返回不同code;

```
LIMIT_PART_COUNT
LIMIT_FILE_SIZE
LIMIT_FILE_COUNT
LIMIT_FIELD_KEY
LIMIT_FIELD_VALUE
LIMIT_FIELD_COUNT
LIMIT_FIELD_COUNT
```


下面就给大家做个简单实例：
html依然不变，js代码如下：

```
const express=require("express");
const multer=require('multer');
var upload=multer({dest:'./upload/',limits:{fileSize: 1024 * 1024 * 20,files: 5}});
var fs = require('fs');

var app=express();

app.use("/",upload.array("files",5),function(req,res,next){
	req.files.forEach(function(ele,index){
		var oldFile=ele.destination+ele.filename;	//指定旧文件
		var newFile=ele.destination+ele.originalname;	//指定新文件
		fs.rename(oldFile,newFile,function(err){
			err?console.log('上传失败！'):console.log('上传成功！');
		});
	});
	next();
	res.send("上传成功！");
});

app.use(function(err,req,res,next){
	if (err.code==='LIMIT_FILE_SIZE'){
		res.send('File is too large');
	}else if(err.code==='LIMIT_FILE_COUNT'){
		res.send('Too many files');
	}
})

app.listen(8080);
```



