---
title: react context
tags: typescript
categories: 前端
date: 2023-11-11 15:38:42
---
<meta name="referrer" content="no-referrer"/>

## 爷孙（远亲组件通信）

```jsx
import {createContext, useContext, useState} from 'react'
import './App.css'

const UserContext = createContext(undefined);

const A = () => {
    return (
        <div>
            this is A
            <B/>
        </div>
    )
}
const B = () => {
    const {count, setCount} = useContext(UserContext);
    console.log(count)
    return (
        <div>
            this is B context props {count}
            <button onClick={() => setCount(count + 1)}>点击修改App组件的context提供的数据</button>
        </div>
    )
}

function App() {
    // 创建state和修改方法
    const [count, setCount] = useState(0)

    return (
        <div>
            {/*提供初始值*/}
            <UserContext.Provider value={{count, setCount}}>
                this is app
                <A/>
            </UserContext.Provider>
        </div>
    )
}

export default App


```

