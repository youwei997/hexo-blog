---
title: nuxt middleware
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:02:39
---
<meta name="referrer" content="no-referrer"/>

## 路由中间件

-   auth.ts

```ts
export default defineNuxtRouteMiddleware((to, from) => {
    console.log("auth", to);

    // if (to.params.id === "1") {
    //     return abortNavigation();
    // }
    // return navigateTo("/");
});
```

## 全局中间件

-   auth.global.ts

```ts
export default defineNuxtRouteMiddleware((to, from) => {
    console.log("auth-global", to);

    // if (to.params.id === "1") {
    //     return abortNavigation();
    // }
    // return navigateTo("/");
});
```

