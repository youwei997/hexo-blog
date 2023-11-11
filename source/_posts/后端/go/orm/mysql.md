---
title: go mysql
tags: go
categories: 后端
date: 2023-11-11 15:13:48
---
<meta name="referrer" content="no-referrer"/>

## 获得连接

```go
package main

import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"time"
)

func main() {
	db, err := sql.Open("mysql", "root:123456@/go_db")
	if err != nil {
		panic(err)
	}
	
	// Close() 释放数据库连接相关的资源
	// 这行要放在err判断下面。因为db有可能会是nil，所以需要确保db不为nil的情况下关闭数据库
	defer db.Close()

	// 数值需要根据业务具体情况来定
	// 最大连接时长
	db.SetConnMaxIdleTime(time.Minute * 3)
	// 最大连接数
	db.SetMaxOpenConns(10)
	// 空闲连接数
	db.SetMaxIdleConns(10)
}

```

## 测试数据库连接

```go
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

func initDB() (err error) {
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_db?charset=utf8mb4&parseTime=True"
	// 不会校验密码是否正确
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return err
	}
	// 尝试与数据库连接
	err = db.Ping()
	if err != nil {
		return err
	}
	return nil
}

func main() {
	err := initDB() // 调用输出数据库函数
	if err != nil {
		fmt.Println("初始化失败")
		// 初始化失败
		return
	} else {
		fmt.Println("初始化成功")
	}
}

```