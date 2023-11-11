---
title: nuxt 连接MongoDB
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:06:24
---
<meta name="referrer" content="no-referrer"/>

## mongodb

1. 安装mongoose

```
npm i mongoose
```

2. 创建连接mongodb服务plugin

```ts
// /server/db/index.ts
import mongoose from "mongoose";

export default async () => {
    try {
        await mongoose.connect("mongodb://localhost:27017/test");
        console.log("数据库连接建立。");
    } catch (err) {
        console.log("数据库连接失败。", err);
    }
};

```

3. 使用 `nuxt.config` 引入插件

```ts
// nuxt.config.ts
// https://v3.nuxtjs.org/api/configuration/nuxt.config
export default defineNuxtConfig({
    typescript: {
        shim: false,
    },
    nitro: {
        plugins: ["~/server/db/index.ts"],
    },
});

```