---
title: vue 响应式api
tags: vue
categories: 前端
date: 2023-11-11 16:12:44
---
<meta name="referrer" content="no-referrer"/>

## ref和reactive

1. 基本类型使用`ref`
   1. `ref`是通过`Object.defineProperty`实现响应式
   2. 如果使用`ref`定义了对象类型，`ref`也会调用 `reactive`来实现响应式
2. 对象类型使用`reactive`。`reactive`本质是通过`es6`的`proxy`来代理对象，实现响应式

## toRef和 toRefs

```html
<script >
import {reactive,ref,toRef,toRefs} from 'vue'
export default {
   setup(){
      const obj = reactive({
         name:'name'
      })

      return {
         // 单个取值
         // 错误！！！
         // 这样相当于把name的值取出来，再赋值给name，就失去响应式，需要使用toRef转换成响应式
         // name:obj.name
         // 用toRef包装过才是响应式
         name:toRef(obj,'name'),

         // reactive 解构
         // 这样解构出来也是响应式
         ...toRefs(obj)
      }
   }
}
</script>
```

## shallowReactive 和 shallowRef

1. `shallowReactive` 包装的对象只有第一层的是响应式
2. `shallowRef` 只处理基本类型，对象类型不处理

```html
<script >
import {shallowReactive,shallowRef,toRef,toRefs} from 'vue'
export default {
   setup(){

      // 正常
      const count = shallowRef(0)
      // 不正常
      const obj2 = shallowRef({
         name:'name'
      })

      // shallowReactive 只处理第一层
      const obj = shallowReactive({
         name:'name'
      })

      return {
         obj
      }
   }
}
</script>
```

## readonly 和 shallowReadonly

1. 使用`readonly`函数包装的响应式对象，无法再修改
2. 使用`shallowReadonly`函数包装的响应式对象，第一层无法再修改，第二层及以下可以修改

## toRaw 和 markRaw

1. `toRaw` 将 `reactive` 生成的响应式数据转成普通对象。只能是 `reactive` 生成的！！！！
2. `markRaw` 标记一个对象不会转换成响应式
   1. 比如在`reactive` 对象里新添加一个对象，不变成响应式，就使用`markRaw`