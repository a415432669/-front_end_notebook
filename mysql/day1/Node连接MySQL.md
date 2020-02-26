# Node.js 连接 MySQL

介绍如何使用 Node.js 来连接 MySQL，并对数据库进行操作。

### 安装驱动

本教程使用了[淘宝定制的 cnpm 命令](https://www.runoob.com/nodejs/nodejs-npm.html#taobaonpm)进行安装：

```
$ cnpm install mysql
```

### 连接数据库

在以下实例中根据你的实际配置修改数据库用户名、及密码及数据库名：

## test.js 文件代码：

```javascript
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',
  password : '123456',
  database : 'test'
});
 
connection.connect();
 
connection.query('SELECT 1 + 1 AS solution', function (error, results, fields) {
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});
```

执行以下命令输出结果为：

$ node test.js
The solution is: 2



## 数据库操作( CURD )

在进行数据库操作前，你需要将本站提供的 Websites 表 SQL 文件[websites.sql](https://static.runoob.com/download/websites.sql) 导入到你的 MySQL 数据库中。

本教程测试的 MySQL 用户名为 root，密码为 123456，数据库为 test，你需要根据自己配置情况修改。

### 查询数据

将上面我们提供的 SQL 文件导入数据库后，执行以下代码即可查询出数据：

## 查询数据

```javascript
var mysql  = require('mysql');  
 
var connection = mysql.createConnection({     
  host     : 'localhost',       
  user     : 'root',              
  password : '123456',       
  port: '3306',                   
  database: 'test' 
}); 
 
connection.connect();
 
var  sql = 'SELECT * FROM websites';
//查
connection.query(sql,function (err, result) {
        if(err){
          console.log('[SELECT ERROR] - ',err.message);
          return;
        }
 
       console.log('------SELECT------');
       console.log(result);
       console.log('------------------');  
});
 
connection.end();
```

执行以下命令输出就结果为：

```
$ node test.js
--------------------------SELECT----------------------------
[ RowDataPacket {
    id: 1,
    name: 'Google',
    url: 'https://www.google.cm/',
    alexa: 1,
    country: 'USA' },
  RowDataPacket {
    id: 2,
    name: '淘宝',
    url: 'https://www.taobao.com/',
    alexa: 13,
    country: 'CN' }
]
------------------------------------------------------------
```

### 插入数据

我们可以向数据表 websties 插入数据：

## 插入数据

```javascript
var mysql  = require('mysql');  
 
var connection = mysql.createConnection({     
  host     : 'localhost',       
  user     : 'root',              
  password : '123456',       
  port: '3306',                   
  database: 'test' 
}); 
 
connection.connect();
 
var  addSql = 'INSERT INTO websites(Id,name,url,alexa,country) VALUES(0,?,?,?,?)';
var  addSqlParams = ['工具', 'https://c.sxb.com','23453', 'CN'];
//增
connection.query(addSql,addSqlParams,function (err, result) {
        if(err){
         console.log('[INSERT ERROR] - ',err.message);
         return;
        }        
 
       console.log('------INSERT------');
       //console.log('INSERT ID:',result.insertId);        
       console.log('INSERT ID:',result);        
       console.log('--------------\n\n');  
});
 
connection.end();
```

执行以下命令输出就结果为：

```
$ node test.js
--------------------------INSERT----------------------------
INSERT ID: OkPacket {
  fieldCount: 0,
  affectedRows: 1,
  insertId: 6,
  serverStatus: 2,
  warningCount: 0,
  message: '',
  protocol41: true,
  changedRows: 0 }
-----------------------------------------------------------------
```

执行成功后，查看数据表，即可以看到添加的数据

### 更新数据

我们也可以对数据库的数据进行修改：

## 更新数据

```javascript
var mysql  = require('mysql');  
 
var connection = mysql.createConnection({     
  host     : 'localhost',       
  user     : 'root',              
  password : '123456',       
  port: '3306',                   
  database: 'test' 
}); 
 
connection.connect();
 
var modSql = 'UPDATE websites SET name = ?,url = ? WHERE Id = ?';
var modSqlParams = ['移动站', 'https://m.sxt.com',6];
//改
connection.query(modSql,modSqlParams,function (err, result) {
   if(err){
         console.log('[UPDATE ERROR] - ',err.message);
         return;
   }        
  console.log('--------UPDATE--------');
  console.log('UPDATE affectedRows',result.affectedRows);
  console.log('----------\n\n');
});
 
connection.end();
```

执行以下命令输出就结果为：

```
--------------------------UPDATE----------------------------
UPDATE affectedRows 1
-----------------------------------------------------------------
```

执行成功后，查看数据表

### 删除数据

我们可以使用以下代码来删除 id 为 6 的数据:

## 删除数据

```javascript
var mysql  = require('mysql');  
 
var connection = mysql.createConnection({     
  host     : 'localhost',       
  user     : 'root',              
  password : '123456',       
  port: '3306',                   
  database: 'test' 
}); 
 
connection.connect();
 
var delSql = 'DELETE FROM websites where id=6';
//删
connection.query(delSql,function (err, result) {
        if(err){
          console.log('[DELETE ERROR] - ',err.message);
          return;
        }        
 
       console.log('-----DELETE-------');
       console.log('DELETE affectedRows',result.affectedRows);
       console.log('----------------\n\n');  
});
 
connection.end();
```

执行以下命令输出就结果为：

```
--------------------------DELETE----------------------------
DELETE affectedRows 1
-----------------------------------------------------------------
```

执行成功后，查看数据表