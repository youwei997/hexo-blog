---
title: go slice
tags: go
categories: 后端
date: 2023-11-11 15:01:20
---
<meta name="referrer" content="no-referrer"/>

## slice 扩展

> slice只能向后扩展！！！

```go
package main

import "fmt"

func main() {
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8}
	s1 := arr[2:6] // len -> [2 3 4 5] cap->[2, 3, 4, 5, 6, 7, 8]
	s2 := s1[3:6]  // len -> [5 6 7] cap->[5, 6, 7, 8]
	s3 := s2[0:4]  // len -> [5 6 7 8] cap->[5, 6, 7, 8]

	// s1 取arr的[2:6] 为 [2 3 4 5] 正常的
	fmt.Println("s1=", s1, "len(s1)=", len(s1), "cap(s1)=", cap(s1)) // s1= [2 3 4 5] len(s1)= 4 cap(s1)= 7
	// s2 能取到s1的[5 6 7] 是因为s1的容量为7 相当于s1为[2, 3, 4, 5, 6, 7, 8]，正常情况s1只能取 [2 3 4 5] ,
	// 但是因为slice扩展的特性，所以可以取超过 [2 3 4 5] 之后的值，但是不能超过最大容量，也就是[2, 3, 4, 5, 6, 7, 8]之外
	// 下面s2 s3 同理。
	fmt.Println("s2=", s2, "len(s2)=", len(s2), "cap(s2)=", cap(s2)) // s2= [5 6 7] len(s2)= 3 cap(s2)= 4
	fmt.Println("s3=", s3, "len(s3)=", len(s3), "cap(s3)=", cap(s3)) // s3= [5 6 7 8] len(s3)= 4 cap(s3)= 4
}

```

### slice只能向后扩展

> slice 下标一旦从前往后取，就无法再向前，容量会越来越少，可越界的也会变少！！！！！！！！

```go
package main

import "fmt"

func main() {
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8}
	s1 := arr[2:4]
	s2 := s1[0:1]
	s3 := s2[0:1]
	s4 := s3[2:4]
	fmt.Println("s1=", s1, "len(s1)=", len(s1), "cap(s1)=", cap(s1)) // s1= [2 3] len(s1)= 2 cap(s1)= 7
	fmt.Println("s2=", s2, "len(s2)=", len(s2), "cap(s2)=", cap(s2)) // s2= [0] len(s2)= 1 cap(s2)= 9
	fmt.Println("s3=", s3, "len(s3)=", len(s3), "cap(s3)=", cap(s3)) // s3= [2] len(s3)= 1 cap(s3)= 7
	fmt.Println("s4=", s4, "len(s4)=", len(s4), "cap(s4)=", cap(s4)) // s4= [4 5] len(s4)= 2 cap(s4)= 5
}
```

## 切片的增删改查

### demo1

```go
package main

import "fmt"

// copy
func copyFunc() {
	// 不copy ，直接赋值 s1 = s2 ，这样改了一个，另一个切片也会改变
	var s1 = []int{1, 2, 3, 4}
	var s2 = make([]int, len(s1))
	copy(s2, s1)
	s2[0] = 100
	fmt.Println(s1)
	fmt.Println(s2)
}

// query
func query() {
	var s1 = []int{1, 2, 3, 4}
	var key = 2
	for i, v := range s1 {
		if v == key {
			fmt.Println("key的下标为", i)
		}
	}
}

// update
func update() {
	var s1 = []int{1, 2, 3, 4}
	s1[1] = 100
	fmt.Println(s1)
}

// del
func del() {
	var s1 = []int{1, 2, 3, 4}
	// 相当于覆盖，从哪个位置覆盖哪些内容
	s1 = append(s1[:2], s1[3:]...)
	fmt.Println(s1)
}

// add
func add() {
	var s1 []int
	s1 = append(s1, 1, 2, 3)
	fmt.Println(s1)
}
func main() {
	copyFunc()
}

```

### demo2

```go
package main

import "fmt"

func main() {
	s1 := []int{0, 1, 2, 3, 4, 5, 6, 7, 8}

	//s9 := []int{9}
	// 增加
	//s2 := append(s1, s9...)
	//fmt.Println(s2)

	// 截取
	// 取第一个
	first := s1[0]
	fmt.Println(first)
	// 取最后一个
	last := s1[len(s1)-1]
	fmt.Println(last)

	// 删除
	// 删除第一个
	delfirst := s1[1:]
	fmt.Println(delfirst)
	// 删除最后一个
	dellast := s1[:len(s1)-1]
	fmt.Println(dellast)

	// 更改
	s1[2] = 9
	fmt.Println(s1)
}

```