---
title: vue setup script语法糖
tags: vue
categories: 前端
date: 2023-11-11 16:14:56
---
<meta name="referrer" content="no-referrer"/>

setup script`语法糖提供了三个新的`API`来供我们使用：`defineProps`、`defineEmit`和`useContext

- **defineProps** 用来接收父组件传来的值`props`。
- **defineEmit**   用来声明触发的事件表。
- **useContext**  用来获取组件上下文`context`。

```vue
<!-- 父组件 -->
<template>
  <div>
    <h2>我是父组件！</h2>
    <Child msg="hello"
           @child-click="handleClick" />
  </div>
</template>

<script setup>
import Child from './components/Child.vue'

const handleClick = (ctx) => {
  console.log(ctx)
}
</script>

```

```vue
<template>
  <span @click="sonClick">msg: {{ props.msg }}</span>
</template>

<script setup>
import { useContext, defineProps, defineEmit } from 'vue'

const emit = defineEmit(['child-click'])
const ctx = useContext()
const props = defineProps({
  msg: String,
})

const sonClick = () => {
  emit('child-click', ctx)
}
</script>

```

