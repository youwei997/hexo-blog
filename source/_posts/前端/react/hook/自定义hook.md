---
title: react 自定义hook
tags: typescript
categories: 前端
date: 2023-11-11 15:34:05
---
<meta name="referrer" content="no-referrer"/>

1. 要以use开头
2. 多次使用一个hook，里面的state不是公用的，是完全隔离的

## 例子1

```js
// 组件
import React from "react";
import useMousePosition from "../hooks/useMousePosition";

const MouseTracker = () => {
  const { x, y } = useMousePosition();
  return (
    <p>
      X: {x} --- y:{y}
    </p>
  );
};
export default MouseTracker;


// 自定义hook文件
// useMousePosition
import { useState, useEffect } from "react";

const useMousePosition = () => {
  const [positions, setPositions] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const updateMouse = (e) => {
      setPositions({
        x: e.x,
        y: e.y,
      });
    };
    window.addEventListener("mousemove", updateMouse);
    // console.log("add");
    // 组件卸载时的操作。这里在返回一个函数，需要卸载相关操作会在返回的函数里处理
    return () => {
      //   console.log("remove");
      window.removeEventListener("mousemove", updateMouse);
    };
  });
  return positions;
};

export default useMousePosition;

```

## 例子2

```jsx
import { useState } from 'react'
function useToggle(){
    // 可复用的逻辑代码
    const [value,setValue] = useState(true)
    const toggle = ()=> setValue(!value)
    // 返回需要在其他组件中使用的状态和函数
    return {
        value,
        toggle
    }
}
export default function App(){
    const {value,toggle} = useToggle()
    return (
    	<div>
        	{value&&<div>this is div</div>}
            <button onClick={toggle}>toggle</button>
        </div>
    )
}
```

