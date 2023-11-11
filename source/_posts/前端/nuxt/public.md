---
title: nuxt public目录
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:00:20
---
<meta name="referrer" content="no-referrer"/>

## 访问 public 目录下文件

重要：`/`就是代表 public 目录。！！！！！！！！！！！！！！！
直接使用/

```html
<template>
    <div class="">
        <p>首页</p>
        <nuxt-link :to="{ path: '/user/1' }">跳转到某个用户信息</nuxt-link>
        <nuxt-link @click="navigate">跳转到about</nuxt-link>
        <img src="/pokemon.png" alt="" />
    </div>
</template>
<script setup lang="ts">
    const navigate = () => {
        return navigateTo({
            path: "/about",
        });
    };
</script>
<style lang="scss"></style>
```