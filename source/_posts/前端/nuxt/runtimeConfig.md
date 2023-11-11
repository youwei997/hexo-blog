---
title: nuxt runtimeConfig
tags: nuxtjs
categories: 前端
date: 2023-11-11 15:59:45
---
<meta name="referrer" content="no-referrer"/>

## nuxt.config.ts配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    // The private keys which are only available within server-side
    apiSecret: '123',
    // Keys within public, will be also exposed to the client-side
    public: {
      apiBase: '/api'
    }
  }
})
```

### 使用

```html
// ts/js or vue
const runtimeConfig = useRuntimeConfig()

console.log(runtimeConfig.apiSecret)
console.log(runtimeConfig.public.apiBase)
```

## env文件配置

```
// .env
NUXT_API_SECRET=api_secret_token
NUXT_PUBLIC_API_BASE=https://nuxtjs.org
```

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    apiSecret: '', // can be overridden by NUXT_API_SECRET environment variable
    public: {
      apiBase: '', // can be overridden by NUXT_PUBLIC_API_BASE environment variable
    },
    // publicApiBase:"",// 和上面public:{apiBase: ''}, 是一样的
  },
})
```

