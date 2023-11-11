---
title: nuxt layout
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:03:05
---
<meta name="referrer" content="no-referrer"/>

## 使用

1. 创建 layouts 文件夹
2. 创建 default.vue 和 custom.vue

```html
// default.vue
<template>
    <div>
        使用layout布局，在app.vue中使用，所有页面都会被包含
        <slot />
    </div>
</template>
```

```html
// custom.vue
<template>
    <div>
        <p>custom layout</p>
        <slot />
    </div>
</template>
```

```html
// app.vue
<template>
    <!-- <NuxtLayout :name="layout">
        <NuxtPage />
    </NuxtLayout> -->
    <NuxtLayout>
        <NuxtPage />
    </NuxtLayout>
</template>

<script setup>
    // You might choose this based on an API call or logged-in status
    const layout = "default";
</script>
```

## 动态更改布局

```html
<template>
    <div>
        <button @click="enableCustomLayout">Update layout</button>
    </div>
</template>

<script setup>
    function enableCustomLayout() {
        setPageLayout("custom");
    }
    definePageMeta({
        layout: false,
    });
</script>
```