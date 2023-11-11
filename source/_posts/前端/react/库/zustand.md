---
title: react zustand使用
tags: typescript
categories: 前端
date: 2023-11-11 15:33:23
---
<meta name="referrer" content="no-referrer"/>

## 安装

```
npm i zustand 
```

## 创建counter

```js
import {create} from "zustand";


export const useStore = create((set) => {
    return {
        // 状态数据
        count: 0,
        // 修改状态数据的方法
        inc: () => {
            set(state => ({count: state.count + 1}))
        }
    }
})
```

## 使用

```jsx
import {useStore} from "./stores/counter.js";

function App() {
    const {count, inc} = useStore()
    return (
        <div>
            <button onClick={inc}>{count}</button>
        </div>
    )
}

export default App
```

