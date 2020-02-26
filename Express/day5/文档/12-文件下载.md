### 文件下载

文件下载非常简单，仅需通过res.download()执行即可，他可以写为3种形式：

```
res.download('/report-12345.pdf');
```

以下是一个对选择对应文件进行下载的实例：
html：

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<form action="http://localhost:8080/" method="post" enctype="application/x-www-form-urlencoded">
			<input type="file" name="files" value="选择下载的文件"><br><br>
			<input type="submit" value="下载">
		</form>
	</body>
</html>
```


js：

```
const express=require("express");
const bodyParser=require("body-parser");

var app=express();

var jsonParser = bodyParser.json();
var urlencodedParser = bodyParser.urlencoded({ extended: false });

app.post('/',urlencodedParser,function(req,res){
	res.download("./public/"+req.body.files,err=>{
		if(err){
			res.send("下载失败！");
		}else{
			console.log("下载成功！");
		}
	});
});

app.listen(8080);
```

我们可以选择根目录public下的文件对其进行下载。

