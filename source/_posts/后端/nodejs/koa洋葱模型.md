---
title: koa洋葱模型
date: 2023-11-11 14:15:08
tags: koa
categories: 前端
---
<meta name="referrer" content="no-referrer"/>

```js
//洋葱模型
const Koa=require('koa');
const app=new Koa();
app.use(async (ctx, next) => {
    console.log('第一1');
    await next(); // 调用下一个middleware
    console.log('第一2');
});

app.use(async (ctx, next) => {
    console.log('第二1');
    await next(); // 调用下一个middleware
    console.log('第二2');
});

app.use(async (ctx, next) => {
    console.log('第三1');
    await next();
    console.log('第三2');
});
app.listen(3000);

输出顺序：
第一1
第二1
第三1
第三2
第二2
第一2
```
