---
title: nodejs原生获取参数
date: 2023-11-11 14:18:17
tags:
categories:
---
<meta name="referrer" content="no-referrer"/>

```js
//req.body
//包含了提交数据的键值对在请求的body中，默认是underfined,
解析body不是nodejs默认提供的，你需要载入body-parser中间件才可以使用req.body；
此方法通常用来解析POST请求中的数据

//原生获取参数
const http = require('http')
app.createServer((req, res) => {
  console.log(req.method);
  if(req.method === 'POST'){
  //数据格式
      console.log('content-type',req.headers['content-type'])
      //接收数据
      let postData = ''
      req.on('data',chunk=>{
          postData+=chunk.toString()
      })
      req.on('end',()=>{
      console.log(postData)
      })
  }
});
server.listen(3000)
```

```js
//req.query
//包含在路由中每个查询字符串参数属性的对象。如果没有，默认为{}
// GET /search?q=tobi+ferret
req.query.q
// => "tobi ferret"

// GET /shoes?order=desc&shoe[color]=blue&shoe[type]=converse
req.query.order
// => "desc"
req.query.shoe.color
// => "blue"
req.query.shoe.type
// => "converse"
```

```js
//req.params
//包含映射到指定的路线“参数”属性的对象。
例如，如果你有route/user/：name，那么“name”属性可作为req.params.name。
该对象默认为{}。
```
