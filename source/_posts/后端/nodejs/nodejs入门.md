---
title: nodejs入门
date: 2023-11-11 14:02:17
tags: nodejs
categories: 前端
---
> web 服务 如何处理一个请求

## url-->网络-->dns 解析-->目标服务器

1. 如何响应这个请求 --> 路由//规则  

## Express

```js
const express = require("express");  
const app = express();  
//app --> application --> web服务的实例  

app.listen(3000, () => {  
  console.log("服务器启动成功");  
});  
```

## 请求的方法来区分

1. get  
2. post  
3. put  
4. delete  
5. ...  

## 通过 uri --> 路径

`/user/getList`  

### 需要定义一个 api/路由，需要满足客户端，无论什么请求方式，请求方法或 uri 都可以得到响应

1. 使用 app.all  
- 任何请求方法都响应  

```js
app.all("/demo", (req, res) => {  
  res.json({  
    msg: "任何请求方法都响应",  
    method: req.method,  
  });  
});  
```

- 任何请求方法及任何 uri 都响应  

```js
//使用通配符  
app.all("*", (req, res) => {  
  res.json({    msg: "任何请求方法及任何uri都响应",  
    method: req.method,    uri: req.path,  });});  
```

2. 使用中间件，无论客户端使用任何 uri，服务端都可以响应 （日志）  
- 使用中间件拦截请求--任何请求方法都响应  

```js
app.use("/middleware", (req, res) => {  
  res.json({  
    msg: "使用中间件拦截请求--任何请求方法都响应",  
    method: req.method,  
    path: req.path,  
  });  
});  
```

- 使用中间件拦截请求--任何请求方法及任何 uri 都响应  

```js
app.use((req, res) => {  
  res.json({  
    msg: "使用中间件拦截请求--任何请求方法及任何uri都响应",  
    method: req.method,  
    path: req.path,  
  });  
});  
```

## 路由

### 路由拆分

1. user  
2. login  
3. register  

### express.Router

1. 创建 routes 目录，存放路由文件  
   
   - 路由文件 如**user**  
     
     ```js
     const express = require("express");   
     const router = express.Router();   
     router.get("/user", (req, res) => {     
         res.json({       
             id: 123,       
             name: "张三",  
     	});   
     });
     ```
2. app.js 使用  
   ```js
   const express = require("express");   
   const userRouter = require("./routes/user");   
   app.use(userRouter);  
   //路由前缀命名空间  
   // app.use('/',userRouter);  
   app.listen(3000, () => {     
       console.log("服务器启动成功");  
   });  
   ```
   
## 中间件

### 是一个函数

```js
// err, req, res, next-->function  
function demo_middleware(err, req, res, next) {  
  // 1. 异常处理  
  // 2. 处理业务功能，然后转交控制权--next  
  // 3. 响应请求--结束响应-->当作路由的处理函数  
}  
```

1. app 级别  
   
   - app 注册时，在最上层  
   - app.use(middleware) app 级别中间件，处理任何请求  
   
   ```
     //下面两个几乎相等  
     // app.all("\*", valid_name_middleware);  
     app.use(valid_name_middleware);   
   ```
   - express 内置中间件  
   ```js  
   //express.static() 第一个参数为和 package.json 同级的一个目录。第二个为配置参数  
   //浏览器输入 localhost:3000/index.html 会显示 static 目录下 index.html 页面  
   //在配置参数配置 extensions: ["html", "htm"], 就可以不写后面的,html。否则会去匹配路由  
   app.use(  
     express.static("static", {       
         // extensions: ["html", "htm"],     
     })   
   );  
   ```
   
2. router 级别  
   
   - 在 app 使用  
     
     ```js
     app.use("/user", userRouter);  
     ```
   - 路由中间件  
     ```js  
     const express = require("express");   
     const router = express.Router(); //路由对象，类似子app  
     router.use((req, res, next) => {     
         console.log("user 路由中间件");  
     	next();   
     });   
     router.get("/demo", (req, res) => {     
         res.json({       
             msg: "user 路由",  
     	});   
     });   
     module.exports = router;  
     ```
3. 异常处理  
   - app 级别异常处理  
   
   - 路由级别异常处理，每个路由使用不同的异常处理