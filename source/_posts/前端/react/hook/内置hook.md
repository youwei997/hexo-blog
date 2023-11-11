---
title: react 内置hook
tags: react
categories: 前端
date: 2023-11-11 15:35:21
---
<meta name="referrer" content="no-referrer"/>

## useState,useEffect

#### watchEffect第二个参数

- 第二个没有参数：组件初始调用一次，组件每次更新时调用（setState时）
- 第二个参数是空数组：组件初始调用一次
- 第二个参数是数组，组件初始调用一次，数组里值改变时重新调用

```js
import React, { useState, useEffect } from "react";

const DogShow = () => {
    const [url, setUrl] = useState("");
    const [loading, setLoading] = useState(false);
    const [fetchState, setFetchState] = useState(false);
    const style = {
        width: 300,
        height: 300,
    };
    useEffect(() => {
        setLoading(true);
        fetch("https://aws.random.cat/meow")
            .then((res) => {
                return res.json();
            })
            .then((res) => {
                setUrl(res.file);
                setLoading(false);
            });
        // 第二个参数是一个数组，如果是空数组就代表，useEffect不依赖props或state，只会初始执行一次
        // 如果不加，useEffect 里使用某个set函数时（组件更新时），useEffect就会重复执行！！！！！！！！！
        // 使用一个值，监听值改变，来控制 useEffect 调用fetch变化，useEffect就重新调用（类似vue的watch）
    }, [fetchState]);

    return (
        <div>
            {loading ? (
                <div>读取中</div>
            ) : (
                <img src={url} alt="dog" style={style} />
            )}
            <button
                onClick={() => {
                    setFetchState(!fetchState);
                }}
            >
                重新获取图片
            </button>
        </div>
    );
};
export default DogShow;
```

#### watchEffect清除副作用

清除副作用适用于清除定时器，子组件有定时器，在父组件卸载子组件时（条件判断），子组件如果没有在watchEffect清除定时器，定时器会一直存在

```jsx
 useEffect(() => {
        const timer = setInterval(()=>{
            console.log('定时器')
            
        },1000)
        return ()=>{
            clearInterval(timer)
        }
    }, []);
```



## useRef （类似 vue 的 ref）

1. 可以定义变量
2. 可以获取 dom 的属性和方法

```js
import React, { useState, useEffect, useRef } from "react";

const FileSearch = ({ title, onFileSearch }) => {
    const [inputActive, setInputActive] = useState(false);
    const [value, setValue] = useState("");

    const inputRef = useRef(null);

    const closeSearch = (e) => {
        console.log("关闭input");
        // 阻止默认事件
        e.preventDefault();
        // 取消input显示
        setInputActive(false);
        // 清空已输入的input value
        setValue("");
    };

    // 监听键盘事件
    useEffect(() => {
        const handleInputEvent = (e) => {
            const { keyCode } = e;
            // 键盘事件 13是enter
            // 是input输入时，并且按下enter
            if (keyCode === 13 && inputActive) {
                console.log("执行enter");
                onFileSearch(value);
            } else if (keyCode === 27 && inputActive) {
                console.log("esc");
                closeSearch(e);
            }
        };
        document.addEventListener("keyup", handleInputEvent);

        // 卸载监听器
        return () => {
            document.removeEventListener("keyup", handleInputEvent);
        };
    });

    // 监听input聚焦
    useEffect(() => {
        // 只有在input显示时聚焦，并且只监听 inputActive 改变时触发
        if (inputActive) {
            inputRef.current.focus();
        }
    }, [inputActive]);

    return (
        <div className="alert alert-primary">
            {!inputActive && (
                <div className="d-flex justify-content-between align-items-center row">
                    <span className="col-8">{title}</span>
                    <button
                        type="button"
                        className="btn btn-primary col-4"
                        onClick={() => {
                            setInputActive(true);
                        }}
                    >
                        搜索
                    </button>
                </div>
            )}
            {inputActive && (
                <div className="row">
                    {/* 这里的 col-8 必须写在外层div上，如果写在input上，就会和form-control样式冲突 */}
                    <div className="col-8">
                        <input
                            ref={inputRef}
                            className="form-control"
                            value={value}
                            onChange={(e) => {
                                setValue(e.target.value);
                            }}
                        />
                    </div>
                    <button
                        type="button"
                        className="btn btn-primary col-4"
                        onClick={closeSearch}
                    >
                        关闭
                    </button>
                </div>
            )}
        </div>
    );
};

export default FileSearch;
```

## useMemo

```js
// 和useEffect 区别：useEffect没有返回值
// 具体看文档
const autofocusNoSpellcheckerOptions = useMemo(() => {
    return {
        autofocus: true,
        spellChecker: false,
        minHeight: "600px",
    };
}, []);
```