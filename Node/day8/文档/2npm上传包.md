# 2. NPM上传包

### 1. 创建文件夹

### 2. npm包的初始化

```
npm init
```

### 3. npm包信息的设置

```json
{
  "name": "lcfs",
  "version": "0.1.0",
  "description": "将原生的fs模块进行promise封装，可以快速的使用async_await模式",
  "main": "lcfs.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "fs",
    "promise封装"
  ],
  "author": "陈鹏",
  "license": "ISC"
}
```

### 4. 注册NPM官网账号

### 5. NPM官网账号需要邮箱验证

### 6. 本机登陆NPM

```
npm login
```

### 7. 发布NPM包

```
npm publish
```

