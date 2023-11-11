---
title: go redis
tags: go
categories: 后端
date: 2023-11-11 15:14:43
---
<meta name="referrer" content="no-referrer"/>

## 连接

```go
package main

import (
	"fmt"
	"github.com/go-redis/redis"
)

// 声明一个全局的rdb变量
var rdb *redis.Client

// 初始化连接
func initClient() (err error) {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "",
		DB:       0, // 使用default db
	})
	_, err = rdb.Ping().Result()
	return err
}

func main() {
	if err := initClient(); err != nil {
		fmt.Println("init redis client failed")
	}
	fmt.Println("connect redis successfully")
	// 程序退出时，释放相关资源
	defer rdb.Close()
}

```

## example

```go
package main

import (
	"fmt"
	"github.com/go-redis/redis"
)

// 声明一个全局的rdb变量
var rdb *redis.Client

// 初始化连接
func initClient() (err error) {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "",
		DB:       0, // 使用default db
	})
	_, err = rdb.Ping().Result()
	return err
}

func redisExample() {
	// key value 超时时间
	err := rdb.Set("score", 100, 0).Err()
	if err != nil {
		fmt.Println("set score failed", err)
		return
	}
	val, err := rdb.Get("score").Result()
	if err != nil {
		fmt.Println("get score failed", err)
		return
	}
	fmt.Println("score", val)
	val2, err := rdb.Get("name").Result()
	// 取redis中不存在的值，报 redis.Nil 错，优先判断这个错
	if err == redis.Nil {
		fmt.Println("name does not exist")
	} else if err != nil {
		fmt.Println("get name failed", err)
		return
	} else {
		fmt.Println("name", val2)
	}
}

func main() {
	if err := initClient(); err != nil {
		fmt.Println("init redis client failed")
	}
	fmt.Println("connect redis successfully")
	// 程序退出时，释放相关资源
	defer rdb.Close()

	redisExample()
}

```