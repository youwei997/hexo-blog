---
title: nuxt server服务器使用
tags: nuxtjs
categories: 前端
date: 2023-11-11 15:57:39
---
<meta name="referrer" content="no-referrer"/>

## 基础使用

-   在中创建一个新文件 server/api/hello.ts：

```ts
export default defineEventHandler((event) => {
    return {
        api: "works",
    };
});
```

-   使用

```html
<template>
    <div class="">
        <p>获取数据</p>
        <nuxt-link to="/">首页</nuxt-link>-
    </div>
</template>
<script lang="ts" setup>
    // const res = await useFetch("/api/hello");
    // console.log(res.data.value.api);

    const { data, error } = await useAsyncData("hello", () => {
        return $fetch("/api/hello"); // 请求地址
    });
    console.log(data.value.api);
</script>
<style lang="scss"></style>
```

## 服务器路由

1. `~/server/api` 在 api 目录下的接口，需要带/api。完整路径:

```
http://localhost:3000/api/hello
```

2. `~/server/routes` 在 api 目录下的接口，不需要带/api。完整路径:

```
http://localhost:3000/hello
```

## 服务器中间件

Nuxt 会自动读入任何文件，~/server/middleware 为你的项目创建服务器中间件。

中间件处理程序将在任何其他服务器路由之前在每个请求上运行，以添加或检查标头、记录请求或扩展事件的请求对象。

```ts
export default defineEventHandler((event) => {
    console.log("New request: " + event.req.url);
});
```

## 使用示例

重要：`server/api/hello.ts`这种路由，默认响应所有请求方法

### 匹配路由参数

服务器路由可以在文件名中使用括号内的动态参数，例如`/api/hello/[name].ts`，并通过`event.context.params`.

```ts
export default defineEventHandler(
    (event) => `获取name请求接口，不分请求方法, ${event.context.params.name}!`
);
```

您现在可以使用`await $fetch('/api/hello/nuxt')`和 get普遍调用此 API `Hello, nuxt!`。

### 匹配 HTTP 方法

句柄文件名可以后缀为`.get`, `.post`, `.put`, `.delete`, ... 以匹配请求的[HTTP Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)。

```ts
// server/test.get.ts
export default defineEventHandler(async (event) => {
    const query = await useQuery(event);
    return `响应 /api/test get请求 ${JSON.stringify(query)}`;
});
```

```ts
// server/test.post.ts
export default defineEventHandler(async (event) => {
    const body = await useBody(event);
    return `响应 /api/test post请求 ${JSON.stringify(body)}`;
});
```

给定上面的例子，获取`/test`：

- **GET**方法：返回`Test get handler`
- **POST**方法：返回`Test post handler`
- 任何其他方法：返回 405 错误

### 包罗万象的路线

包罗万象的路由有助于回退路由处理。例如，创建一个名为的文件`~/server/api/foo/[...].ts`将为所有不匹配任何路由处理程序的请求注册一个包罗万象的路由，例如`/api/foo/bar/baz`.

```ts
// server/api/foo/[...].ts
export default defineEventHandler(() => `Default foo handler`)
```

```ts
// server/api/[...].ts
export default defineEventHandler(() => `Default api handler`)
```

```ts
// server/api/all/[...].ts
export default defineEventHandler(
    () => `/api/all/*，所有请求接口和请求方法都响应`
);
```

### 使用正文处理请求 （获取body）

```ts
// server/api/submit.post.ts
export default defineEventHandler(async (event) => {
    const body = await useBody(event)
    return { body }
})
```

您现在可以使用`$fetch('/api/submit', { method: 'post', body: { test: 123 } })`.

<div style="background:rgba(255,251,235,1);color:rgba(217,119,6,1);padding:10px">
我们`submit.post.ts`在文件名中使用仅用于将请求与`POST`可以接受请求正文的方法匹配。`useBody`在GET 请求中使用时，`useBody`会抛出`405 Method Not Allowed`HTTP 错误。
</div>


### 使用查询参数处理请求

示例查询`/api/query?param1=a&param2=b`

```ts
// server/api/query.get.ts
export default defineEventHandler((event) => {
  const query = useQuery(event)
  return { a: query.param1, b: query.param2 }
})
```