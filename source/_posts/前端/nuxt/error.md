---
title: nuxt error
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:03:31
---
<meta name="referrer" content="no-referrer"/>

## 404 页面

1. 创建 `~/error.vue`

```html
<template>
    <button @click="handleError">Clear errors</button>
</template>

<script setup>
    const props = defineProps({
        error: Object,
    });

    const handleError = () => clearError({ redirect: "/" });
</script>
```

