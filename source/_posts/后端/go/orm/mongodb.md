---
title: go mongodb
tags: go
categories: 后端
date: 2023-11-11 15:12:48
---
<meta name="referrer" content="no-referrer"/>

## 连接

```go
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
)

var client *mongo.Client

func initDB() {
	// 设置客户端连接配置
	clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")
	// 连接到mongodb
	var err error
	client, err = mongo.Connect(context.TODO(), clientOptions)
	if err != nil {
		log.Fatal(err)
	}
	// 检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("connected to MongoDB")
}
func main() {
	initDB()
}

```