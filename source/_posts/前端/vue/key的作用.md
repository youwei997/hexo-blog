---
title: vue key的作用
tags: vue
categories: 前端
date: 2023-11-11 16:19:20
---
<meta name="referrer" content="no-referrer"/>

## Vue 中 v-if 和 v-for 为何不能连用

```js
v-for优先执行，会创建对应的dom节点，如果v-if为false，会删除这个dom节点；这样创建后再删除，会造成页面卡顿。
// 可以在v-for里面用v-if只显示符合条件的数组数据，但效率会低，所以一般是先提取需要的数据再用v-for
```

## vue 中:key 的作用

https://www.jianshu.com/p/5d771cf57012

使用 key <br>
![](https://upload-images.jianshu.io/upload_images/13201627-c3e12cdb02d59c24.png?imageMogr2/auto-orient/strip|imageView2/2/w/477/format/webp)

不使用 key，则 diff 算法默认是这样的： <br>
![](https://upload-images.jianshu.io/upload_images/13201627-9d6226c6268a341b.png?imageMogr2/auto-orient/strip|imageView2/2/w/572/format/webp)

即把 C 更新成 F，D 更新成 C，E 更新成 D，最后再插入 E，是不是很没有效率？ 更新了 3 次，之后做了一次创建插入的操作
所以我们需要使用 key 来给每个节点做一个唯一标识，Diff 算法就可以正确的识别此节点，找到正确的位置区插入新的节点。

![](https://upload-images.jianshu.io/upload_images/13201627-d0b3a1577860fda9.png?imageMogr2/auto-orient/strip|imageView2/2/w/452/format/webp)