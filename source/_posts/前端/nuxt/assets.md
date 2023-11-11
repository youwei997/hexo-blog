---
title: nuxt assets
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:04:43
---
<meta name="referrer" content="no-referrer"/>

## 访问 assets 目录下文件

使用`~/assets`

```html
<template>
    <div class="">
        <p>about</p>
        <nuxt-link to="/">回首页</nuxt-link>
        <img src="~/assets/pokemon.png" alt="" />
    </div>
</template>
<script lang="ts" setup></script>
<style lang="scss"></style>
```

