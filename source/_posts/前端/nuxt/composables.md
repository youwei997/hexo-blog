---
title: nuxt Composables 自定义hook
tags: nuxtjs
categories: 前端
date: 2023-11-11 16:04:14
---
<meta name="referrer" content="no-referrer"/>

## 创建

1. 使用`export`

```ts
// composables/useFoo.ts 
export const useFoo = () => {
  return useState('foo', () => 'bar')
}
```

2. 使用 `export default`

```js
// composables/useFoo.ts 
export default function () {
  return useState('foo', () => 'bar')
}
```



## 使用

```html
<template>
  <div>
    {{ foo }}
  </div>
</template>

<script setup>
const foo = useFoo()
</script>
```

