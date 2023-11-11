---
title: vue vue-router
tags: vue
categories: 前端
date: 2023-11-11 16:11:19
---
<meta name="referrer" content="no-referrer"/>

## 全局前置守卫 beforeEach 

- 每一次跳转路由之前调用

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

## 全局解析守卫 beforeResolve 

## 全局后置钩子 afterEach

- 每一次跳转路由之后调用

```js
router.afterEach((to, from) => {
  // ...
})
```

## 路由独享的守卫 beforeEnter 

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

## 组件内的守卫

1. 通过行为跳转触发

### beforeRouteEnter

### beforeRouteUpdate 

### beforeRouteLeave

```javascript
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```