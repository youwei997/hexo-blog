---
title: go map
tags: go
categories: 后端
date: 2023-11-11 15:02:05
---
<meta name="referrer" content="no-referrer"/>

```go
package main

import "fmt"

func mapFunc() {
	m1 := make(map[string]string)
	m1["name"] = "tom"
	// 判断某个值在map里是否存在
	v1, ok := m1["name"]
	fmt.Println(v1, ok)

	// 不存在某个值，value会是个空字符串，ok为false
	v2, ok := m1["age"]
	fmt.Println(v2, ok)
}
func mapFor() {
	// map 遍历是无序的
	m1 := map[string]string{"name": "tom", "age": "12", "email": "tom@example"}
	for k, v := range m1 {
		fmt.Println(k, v)
	}
}

func main() {
	mapFunc()
}
```

