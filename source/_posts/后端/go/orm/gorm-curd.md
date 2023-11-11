---
title: go gorm-curd
tags: go
categories: 后端
date: 2023-11-11 15:11:48
---
<meta name="referrer" content="no-referrer"/>

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

type Product struct {
	gorm.Model
	Code  string
	Price uint
}

// 创建表
func create(db *gorm.DB) {
	db.AutoMigrate(&Product{})
}

// 插入数据
func insert(db *gorm.DB) {
	p := Product{
		Code:  "4",
		Price: 12,
	}
	db.Create(&p)
}

func query(db *gorm.DB) {
	var product Product
	db.First(&product, 2) // 查找主键
	fmt.Println(product)
	//db.First(&product, "code = ?", "D42") // 查找商品代码
}

// 更新
func update(db *gorm.DB) {
	var product Product
	db.First(&product, 2)
	db.Model(&product).Update("Price", 1001)
}

// 删除(软删除)
func del(db *gorm.DB) {
	var p Product
	db.Delete(&p, 1)
}

func main() {
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic(err)
	}

	//insert(db)
	//query(db)
	//update(db)
	del(db)
}

```