---
title: nuxt seo和meta
tags: nuxtjs
categories: 前端
date: 2023-11-11 15:58:36
---
<meta name="referrer" content="no-referrer"/>

## 设置 Head 属性

1. 使用`<Title>`等元组件

```html
<template>
    <div class="">
        <p>首页</p>
        <!-- 会被markdown格式化成小写，先注释 -->
        <!-- <Title>nuxt 标题</Title> -->
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

2. 使用 `useHead` hook

```html
<template>
    <div class="">
        <nuxt-link to="/"><p>首页</p></nuxt-link>
        <!-- <Title>nuxt 标题</Title> -->
    </div>
</template>
<script setup lang="ts">
    useHead({
        title: "title use useHead",
    });
</script>
<style lang="scss"></style>
```

3. 使用 js

```html
<template>
    <div class="">
        <nuxt-link to="/"><p>首页</p></nuxt-link>
        <!-- <Title>nuxt 标题</Title> -->
    </div>
</template>
<script setup lang="ts">
    useHead({
        title: "title use useHead",
        script: [
            {
                // 本地js路径
                src: "/a.js",
                // 把放在body里
                // body如果是false，在引入的js里，会取不到dom元素
                body: true,
            },
        ],
    });
</script>
<style lang="scss"></style>
```