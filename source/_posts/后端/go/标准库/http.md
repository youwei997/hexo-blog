---
title: go 标准库 http
tags: go
categories: 后端
date: 2023-11-11 15:15:32
---
<meta name="referrer" content="no-referrer"/>

## get

```go
package main

import (
	"encoding/json"
	"fmt"
	"io"
	"io/ioutil"
	"log"
	"net/http"
	"net/url"
)

// 普通请求
func testGet() {
	url := "https://aws.random.cat/meow"
	r, err := http.Get(url)
	if err != nil {
		log.Fatal(err)
	}
	defer r.Body.Close()
	b, _ := ioutil.ReadAll(r.Body)
	fmt.Println(string(b))
}

// 手动设置参数
func testGet2() {
	params := url.Values{}
	Url, err := url.Parse("http://82.157.150.149:3000/search")
	if err != nil {
		return
	}
	params.Set("keywords", "海阔天空")
	// 有中文 编码一下
	Url.RawQuery = params.Encode()
	urlPath := Url.String()
	fmt.Println(urlPath)
	resp, err := http.Get(urlPath)
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
	fmt.Println(string(body))
}

// 解析json
func test3() {
	type Result struct {
		Result map[string]interface{} `json:"result"`
		Code   int                    `json:"code"`
	}
	resp, err := http.Get("http://82.157.150.149:3000/search?keywords=%E6%B5%B7%E9%98%94%E5%A4%A9%E7%A9%BA")
	if err != nil {
		return
	}
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
	var result Result
	_ = json.Unmarshal(body, &result)
	fmt.Println(result)
}

// httpServer
func httpServer() {
	// 请求处理函数
	f := func(resp http.ResponseWriter, req *http.Request) {
		io.WriteString(resp, "hello world")
	}
	// 响应路径 前面需要加 /
	http.HandleFunc("/hello", f)
	// 设置监听端口，并监听，前面需要有冒号 :
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		log.Fatal(err)
	}
}

func main() {
	//testGet()
	//testGet2()
	//test3()
	httpServer()
}


```