---
title: go defer
tags: go
categories: 后端
date: 2023-11-11 15:02:33
---
<meta name="referrer" content="no-referrer"/>

```go
package main

import "fmt"

func main() {
	fmt.Println("start")
	defer fmt.Println("step1")
	defer fmt.Println("step2")
	defer fmt.Println("step3")
	fmt.Println("end")
	
	/*
	start
	end
	step3
	step2
	step1

	*/
}

```

