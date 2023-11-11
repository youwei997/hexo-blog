---
title: nuxt plugin
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:01:36
---
<meta name="referrer" content="no-referrer"/>

-   在`plugins`目录（没有就创建）下创建创建 js/ts

```ts
export default defineNuxtPlugin(() => {
    return {
        provide: {
            hello: (msg: string) => `Hello ${msg}!`,
        },
    };
});
```

-   使用

```html
<template>
    <div class="">
        <p>首页</p>
        <nuxt-link :to="{ path: '/user/1' }">跳转到某个用户信息</nuxt-link>
        <nuxt-link @click="navigate">跳转到about</nuxt-link>
        <img src="/pokemon.png" alt="" />

        <!-- 使用插件 -->
        <p>{{ $hello("使用plugin输出文本") }}</p>
    </div>
</template>
<script setup lang="ts">
    const navigate = () => {
        return navigateTo({
            path: "/about",
        });
    };

    // 使用插件
    const { $hello } = useNuxtApp();
</script>
<style lang="scss"></style>
```