---
title: vue 结合ts
tags: vue
categories: 前端
date: 2023-11-11 16:17:36
---
<meta name="referrer" content="no-referrer"/>

## defineProps & defineEmits

注：在使用了 `script setup` 如果script里不需要用到props可以不接收，因为在模板里可以直接使用父级传递的值，无需`props.xxx`!!!!!!!!!

无ts用法：

```vue
<script setup>
// 数组
const props = defineProps(['foo'])
console.log(props.foo)
    
// 对象
const props = defineProps({
  title: String,
  likes: Number
})
console.log(props.foo)
</script>
```

以下组件使用ts：

1. `defineEmits`：使用了`v-model`加 `update`结合使用，这样无需在父组件在写一个方法接收，调用修改值
2. `defineProps`：子组件使用了 泛型参数来定义 props 的类型 

```vue
// 父组件
<script setup lang="ts">
import NavHeader from '@/components/container/navHeader/index.vue'
import NavSide from '@/components/container/navSide/index.vue'

import { ref } from 'vue'

const isCollapse = ref(false)
</script>

<template>
  <div class="common-layout">
    <el-container>
      <el-aside width="auto">
        <NavSide :collapse="isCollapse" />
      </el-aside>
      <el-container>
        <el-header>
          <NavHeader v-model:collapse="isCollapse" />
        </el-header>
      </el-container>
    </el-container>
  </div>
</template>

<style scoped></style>

```

```vue
// 子组件1
<script setup lang="ts">
const props = defineProps<{ collapse: boolean }>()
const emits = defineEmits(['update:collapse'])
const toggle = () => {
  emits('update:collapse', !props.collapse)
}
</script>

<template>
  <span @click="toggle">
    <el-icon-expand v-if="collapse"></el-icon-expand>
    <el-icon-fold v-else></el-icon-fold>
  </span>
</template>

<style scoped></style>

```

```vue
// 子组件2
<script setup lang="ts">

const props = defineProps<{ collapse: boolean }>()
</script>

<template>
  <el-menu class="el-menu-vertical-demo" :collapse="collapse" default-active="1">
    <el-menu-item index="1">
      <el-icon><el-icon-menu /></el-icon>
      <span>导航1</span>
    </el-menu-item>
    <el-menu-item index="2">
      <el-icon><el-icon-menu /></el-icon>
      <span>导航2</span>
    </el-menu-item>
    <el-menu-item index="3">
      <el-icon><el-icon-menu /></el-icon>
      <span>导航3</span>
    </el-menu-item>
  </el-menu>
</template>

<style scoped>
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
}
</style>

```

