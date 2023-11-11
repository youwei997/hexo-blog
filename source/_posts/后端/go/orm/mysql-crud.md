---
title: go mysql-crud
tags: go
categories: 后端
date: 2023-11-11 15:14:12
---
<meta name="referrer" content="no-referrer"/>

> 使用了fiber框架

```go
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"github.com/gofiber/fiber/v2"
)

var db *sql.DB

// 初始化数据库
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

// 插入数据
func insertData() {
	sqlStr := "insert into user_tbl(username,password) values(?,?)"
	ret, err := db.Exec(sqlStr, "zs", "123456")
	if err != nil {
		fmt.Println(err)
		return
	}
	theID, err := ret.LastInsertId()
	if err != nil {
		fmt.Println("get lastinsert id failed")
		return
	}
	fmt.Println(theID)
}

type user struct {
	id       int
	username string
	password string
}

// 查询单条
func queryRow() {
	sqlStr := "select id,username,password from user_tbl where id=?"
	var u user
	err := db.QueryRow(sqlStr, 1).Scan(&u.id, &u.username, &u.password)
	if err != nil {
		fmt.Println("scan failed")
		return
	}
	fmt.Println(u.id, u.username, u.password)
}

// 查询多条
func queryManyRow() {
	sqlStr := "select * from user_tbl"
	r, err := db.Query(sqlStr)
	defer r.Close()
	if err != nil {
		fmt.Println(err)
		return
	}
	for r.Next() {
		var u user
		err := r.Scan(&u.id, &u.username, &u.password)
		if err != nil {
			fmt.Println("scan failed")
			return
		}
		fmt.Println(u.id, u.username, u.password)
	}
}

// 更新
func updateData() {
	sqlStr := "update user_tbl set username=?,password=? where id=?"
	ret, err := db.Exec(sqlStr, "update", "updatepassword", 1)
	if err != nil {
		fmt.Println("更新失败")
		return
	}
	rows, err := ret.RowsAffected()
	if err != nil {
		fmt.Println("更新行失败")
		return
	}
	fmt.Println("更新成功，更新的行数=", rows)
}

// 删除
func deleteData() {
	sqlStr := "delete from user_tbl where id=?"
	ret, err := db.Exec(sqlStr, 3)
	if err != nil {
		fmt.Println("删除失败")
		return
	}
	rows, err := ret.RowsAffected()
	if err != nil {
		fmt.Println("删除行失败")
		return
	}
	fmt.Println("删除成功，删除的行=", rows)
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

	app := fiber.New()
	app.Get("/", func(c *fiber.Ctx) error {
		return c.SendString("Hello, World!")
	})

	app.Post("/insert", func(c *fiber.Ctx) error {
		insertData()
		return c.Send(c.Body())
		//return c.SendString("I'm a POST request!")
	})
	app.Get("/query", func(c *fiber.Ctx) error {
		queryRow()
		return c.SendString("query")
	})
	app.Get("/queryMany", func(c *fiber.Ctx) error {
		queryManyRow()
		return c.SendString("queryMany")
	})
	app.Get("/updateData", func(c *fiber.Ctx) error {
		updateData()
		return c.SendString("updateData")
	})
	app.Get("/deleteData", func(c *fiber.Ctx) error {
		deleteData()
		return c.SendString("deleteData")
	})

	app.Listen(":3000")
}

```