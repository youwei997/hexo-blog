---
title: nodejs常用工具库
date: 2023-11-11 14:19:14
tags:
categories:
---
<meta name="referrer" content="no-referrer"/>

## 密码加密

1. bcrypt
   
   - 加密
   
     + hash （异步）
   
   
     + hashSync （同步）第一个为加密的值（密码），第二个为加密的等级
   
   ```js
   const bcrypt = require("bcrypt");
   
   const UserSchema = new mongoose.Schema({
     username: { 
       type: String,
       unique: true, //限唯一值
     },
     password: {
       type: String,
       set(value) { //在这里使用 bcrypt.hashSync 加密（散列）密码
         return bcrypt.hashSync(value, 1);
           //加密后 $2b$04$ziX.qnWtqjnnwrYjTEG.tuYGmQXCgK8PjjFn3VjWJo/2pBpaqTye6
       },
     },
   });
   const User = mongoose.model("User", UserSchema);
   module.exports = { User };
   ```
   
   - ​	对比
   
     - compare （异步）
     - compareSync（同步） 第一个为接口传入，第二个为数据库取出。返回`boolean`
   
     ```js
     app.post("/api/login", async (req, res) => {
       const { username, password } = req.body;
       const user = await User.findOne({
         username,
       });
       if (!user) {
         return res.status(422).send({ msg: "用户名不存在" });
       }
       const isPasswordValid = bcrypt.compareSync(password, user.password);
       console.log(isPasswordValid);// true/false
       res.send(user);
     });
     ```
   
     

## token

1. jsonwebtoken

   - jwt.sign({ id: String(user._id) }, SECRET,option)  第一个参数为id等可以查找对应用户的数据（对象）

     第二个为`SECRET`一般放在.env等被gitignore忽略的文件中，第三个为配置项（过期时间等）：`{expiresIn:3600}`,第四个为回调函数

   ```js
   const jwt = require("jsonwebtoken");
   const SECRET = "secret";
   
   app.post("/api/login", async (req, res) => {
     const { username, password } = req.body;
     //查询用户
     const user = await User.findOne({
       username,
     });
     //用户是否存在
     if (!user) {
       return res.status(422).send({ msg: "用户名不存在" });
     }
     //校验密码
     const isPasswordValid = bcrypt.compareSync(password, user.password);
     if (!isPasswordValid) {
       return res.status(422).send({ msg: "密码无效" });
     }
     //生成token
     const token = jwt.sign({ id: String(user._id) }, SECRET); //eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjYyNGE2MmNmYjNmMDg4MmUyMmY1NmMyMiIsImlhdCI6MTY0OTA0OTM0OX0.O0poGl9KpakXAN9v0CDDiJXrEGqNpzRTYjppkn9Uvs8
     
       res.send({
       user,
       token,
     });
   });
   ```

   - jwt.verify(token, SECRET) 第一个参数为前端传来的`token`，第二个为`SECRET`

   ```js
   const jwt = require("jsonwebtoken");
   const SECRET = "secret";
   app.get("/api/profile", async (req, res) => {
    let authorization = req.headers.authorization;
    const token = String(authorization).split(" ")[1];
    // const tokenVerify = jwt.verify(token, SECRET); //{ id: '624a62cfb3f0882e22f56c22', iat: 1649049349 }
    const { id } = jwt.verify(token, SECRET);
    const user = await User.findById(id);
    res.send(user);
   });
   
   ```

   - 验证token是否过期并规定哪些路由不用验证，需要npm 引入 express-jwt

   ```js
   const expressJwt = require('express-jwt')
   // app.use(
   //   expressJwt({
   //     secret: "mes_qdhd_mobile_xhykjyxgs",
   //   }).unless({
   //     path: ["/login"], //除了这个地址，其他的URL都需要验证
   //   })
   // );
   ```

2. passport & passport-jwt
   1. 具体使用看这个项目`https://gitee.com/youwei997/nodejs-element-wuhaiyang`

## 跨域

1. cors

2. 手动封装跨域中间件

   1. 接口形式

   ```js
   const express = require("express");
   const app = express();
   
   //设置跨域访问
   app.all("*",function(req,res,next){
       //设置允许跨域的域名，*代表允许任意域名跨域
       res.header("Access-Control-Allow-Origin","*");
       //允许的header类型
       res.header("Access-Control-Allow-Headers","content-type");
       //跨域允许的请求方式 
       res.header("Access-Control-Allow-Methods","DELETE,PUT,POST,GET,OPTIONS");
       if (req.method.toLowerCase() == 'options')
           res.send(200);  //让options尝试请求快速结束
       else
           next();
   })
   ```

   2. 全局中间件形式

   ```js
   // 自定义跨域中间件
   var allowCors = function(req, res, next) {
       res.header('Access-Control-Allow-Origin', req.headers.origin);
       res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
       res.header('Access-Control-Allow-Headers', 'Content-Type');
       res.header('Access-Control-Allow-Credentials','true');
       next();
   };
   app.use(allowCors);//使用跨域中间件
   ```
   
3. cors 第三方中间件

   ```js
   const express = require("express");
   const app = express();
   
   const cors = require("cors");
   
   app.use(cors()); //允许跨域
   
   app.get("/", (req, res) => {
     res.send("ok");
   });
   
   app.listen(3000, () => {
     console.log("服务端启动");
   });
   ```

   

## 参数解析

1. **内置** `express.json & express.urlencoded({ extended: false })`

```js
app.use(express.urlencoded({ extended: false })); //x-www-form-urlencoded
app.use(express.json()); //使用ui框架，发送纯json时有用，或者postman body raw。使用postman x-www-form-urlencoded无效
```

2.  **第三方** body-parser

```js
const express = require("express");
const bodyParser = require("body-parser");
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

