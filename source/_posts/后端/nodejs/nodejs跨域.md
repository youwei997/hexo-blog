---
title: nodejs跨域
date: 2023-11-11 14:16:10
tags:
categories:
---
<meta name="referrer" content="no-referrer"/>

## koa

```js
//设置跨域访问
app.use(async (ctx, next) => {
    ctx.set('Access-Control-Allow-Origin', '*')
    ctx.set('Access-Control-Allow-Headers', 'Content-Type,Content-Length,Authorization,Accept,X-Requested-With')
    ctx.set('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS')
    if (ctx.method == 'OPTIONS') {
        ctx.body = 200;
    } else {
        await next()
    }
})
```

## express

```js
//设置跨域访问
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1')
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});
```

