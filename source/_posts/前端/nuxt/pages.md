---
title: nuxt pages
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:02:13
---
<meta name="referrer" content="no-referrer"/>

## 路由

-   访问 http://localhost:3000/about`
-   文件夹 `/pages/about.vue`
-   文件 `about.vue`

## 动态路由

### id 访问

-   访问 http://localhost:3000/user/1
-   文件夹 `/pages/user/[id].vue`
-   文件 `[id].vue`

### 动态路由默认页面

-   文件夹`/pages/user/index.vue`
-   文件 `index.vue`

### 获取 参数

#### 获取 params

1. 使用`$route.params`获取
2. 使用`const route = useRoute();`函数获取

#### 获取 query

1. 使用`$route.query`获取
2. 使用`const route = useRoute();`函数获取

```html
<template>
    <div class="">{{ $route.params }} {{ $route.query }}</div>
</template>
<script lang="ts" setup>
    const route = useRoute();
    console.log(route.params);
    console.log(route.query);
</script>
<style lang="scss"></style>
```

## 跳转

### nuxt-link

```html
<template>
    <div class="">
        <p>首页</p>
        <nuxt-link :to="{ path: '/user/1' }">跳转到某个用户信息</nuxt-link>
    </div>
</template>
<script setup lang="ts"></script>
<style lang="scss"></style>
```