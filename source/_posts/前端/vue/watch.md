---
title: vue watch
tags: vue
categories: 前端
date: 2023-11-11 16:18:00
---
<meta name="referrer" content="no-referrer"/>

## watch

```html
<script setup>
import { ref } from 'vue'
const sum = ref(0)

/* 监听ref 基本类型 */
// ！！！！！！！！！！不能这么监听，因为这样就相当于监听0值
// watch(sum.value,(newVal,oldVal)=>{
//     console.log(newVal,oldVal)
// })

// 正确监听
watch(()=>sum.value,(newVal,oldVal)=>{
    console.log(newVal,oldVal)
})

/* 监听ref 对象类型 */
const obj = ref({
    name:'张三'
})

// 正确监听
// 这里的监听相当于监听ref 里面的reactive对象。而不是ref基本类型的值
watch(obj.value,(newVal,oldVal)=>{
    console.log(newVal,oldVal)
})

</script>
```

## watchEffect

1. `watchEffect` 回调里，用到了什么数据，就监听什么数据

```html
<script setup>
import {ref,watchEffect} from 'vue'
const count = ref(0)

watchEffect(()=>{
    console.log('watchEffect')
})

</script>
```