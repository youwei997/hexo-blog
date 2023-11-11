---
title: vue 使用axios
tags: vue
categories: 前端
date: 2023-11-11 16:14:15
---
<meta name="referrer" content="no-referrer"/>

```javascript
//main.js引入axios或封装的js，然后使用globalProperties api挂在全局
import request from './utils/request'
//全局配置
app.config.globalProperties.$request = request;
//页面使用
//setup中使用getCurrentInstanceAPI获取全局对象
//在获取proxy，解构出
const {proxy} = getCurrentInstance()
onMounted(() => {
   console.log(proxy)
})
```

