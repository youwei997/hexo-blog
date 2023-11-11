---
title: mongoose，findOne查询使用变量做字段名
date: 2023-11-11 14:12:19
tags: mongoose
categories: 前端
---
<meta name="referrer" content="no-referrer"/>

1. 使用新建对象
```js
const validateCustom = async (type, val) => {
  const obj = {};
  obj[type] = val;
  const validate = await User.findOne(obj);

  // 同上
  // const validate = await User.findOne({ [type]: val });

  if (validate) {
    return;
  }
};
```
2. 使用where
```js
const validateCustom = async (type, val) => {
  const validate = await User.find().where(type, val);
  if (validate.length > 0) {
    return;
  }
};
```
