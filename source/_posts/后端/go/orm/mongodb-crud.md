---
title: go mongodb-crud
tags: go
categories: 后端
date: 2023-11-11 15:13:22
---
<meta name="referrer" content="no-referrer"/>

## example1

```go
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
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
		return
	}
	fmt.Println("connected to MongoDB")
}

type Student struct {
	Name string
	Age  int
}

func insertOne() {
	initDB()
	s := Student{
		Name: "tom",
		Age:  10,
	}
	var collection = client.Database("go_db").Collection("student")
	insertResult, err := collection.InsertOne(context.TODO(), s)
	if err != nil {
		log.Fatal(err)
		return
	}
	fmt.Println("插入成功，数据id=", insertResult.InsertedID)
}

func insertMany() {
	initDB()
	var collection = client.Database("go_db").Collection("student")
	s1 := Student{
		Name: "tom11",
		Age:  11,
	}
	s2 := Student{
		Name: "tom21",
		Age:  12,
	}

	stus := []interface{}{s1, s2}
	imr, err := collection.InsertMany(context.TODO(), stus)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(imr.InsertedIDs)
}

func find() {
	initDB()
	ctx := context.TODO()
	defer client.Disconnect(ctx)
	var collection = client.Database("go_db").Collection("student")
	//cur, err := collection.Find(ctx, bson.D{})
	cur, err := collection.Find(ctx, bson.D{{"name", "tom"}}) // 查找name是tom的
	if err != nil {
		log.Fatal(err)
	}
	defer cur.Close(ctx)
	for cur.Next(ctx) {
		var result bson.D
		err := cur.Decode(&result)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Println("result", result)
		fmt.Println("result.Map", result.Map())
	}
	if err := cur.Err(); err != nil {
		log.Fatal(err)
	}
}

func updateMany() {
	initDB()
	ctx := context.TODO()
	defer client.Disconnect(ctx)
	var collection = client.Database("go_db").Collection("student")
	// 修改的值
	update := bson.D{{"$set", bson.D{{"name", "update"}, {"age", "99"}}}}
	// 哪条数据被修改
	ur, err := collection.UpdateMany(ctx, bson.D{{"name", "tom"}}, update)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Updated count", ur.ModifiedCount)
}

func deleteMany() {
	initDB()
	ctx := context.TODO()
	var collection = client.Database("go_db").Collection("student")
	dr, err := collection.DeleteMany(ctx, bson.D{{"name", "tom1"}})
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Deleted count", dr.DeletedCount)
}

func main() {
	//insertOne(s)

	//insertMany()

	//find()

	//updateMany()

	deleteMany()
}

```

## example2

```go
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
	"time"
)

func main() {
	// mongodb 连接配置
	client, err := mongo.NewClient(options.Client().ApplyURI("mongodb://localhost:27017"))

	// 判断错误
	if err != nil {
		fmt.Errorf("连接错误")
	}

	// 超时控制
	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()

	// 创建连接
	if err = client.Connect(ctx); err == nil {
		fmt.Println("Connect succeeded")
	}

	// 写入一行数据
	//insertDoc(client, ctx)

	// 写入多行
	//insertMany(client, ctx)

	// 更新一行
	//updateDoc(client, ctx)

	// 查询
	//findDoc(client, ctx)

	// 删除一行
	//deleteDoc(client, ctx)

	// 删除多条
	deleteManyDoc(client, ctx)

	/*
		// 测试
		// 连接database
		db := client.Database("test")

		// 获取集合名
		collectionNames, err := db.ListCollectionNames(ctx, bson.M{})

		// 输出集合名
		fmt.Println("collectionNames", collectionNames)
	*/

	// 断开连接
	defer func() {
		if err = client.Disconnect(ctx); err != nil {
			panic(err)
		}
	}()
}

// 写入文档
func insertDoc(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 写入一行数据
	insertOneResult, err := collection.InsertOne(ctx, bson.M{"name": "张三", "age": 18})
	if err != nil {
		log.Fatal(err)
	}

	// 输出写入文档的id
	fmt.Println("id", insertOneResult.InsertedID)
}

// 写入多行
func insertMany(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 写入多行数据
	docs := []interface{}{
		bson.M{"name": "李四", "age": 18},
		bson.M{"name": "二狗", "age": 78},
	}

	// 指定是否顺序写入，默认是true，一行报错中断后面的操作。设为false可以跳过报错的
	insertManyOpts := options.InsertMany().SetOrdered(false)

	// 插入多行
	insertManyResult, err := collection.InsertMany(ctx, docs, insertManyOpts)

	if err != nil {
		log.Fatal(err)
	}

	// 输出所有写入记录的id
	fmt.Println("ids", insertManyResult.InsertedIDs)
}

// 更新文档
func updateDoc(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 如果更新的文档不存在，则插入新的文档
	updateOneOpts := options.Update().SetUpsert(true)

	// 更新的条件
	updateOneFilter := bson.M{"name": "张三"}

	// 更新后的值
	updateOneSet := bson.M{"$set": bson.M{"name": "帅哥"}}

	// 更新操作
	updateOneResult, err := collection.UpdateOne(ctx, updateOneFilter, updateOneSet, updateOneOpts)
	if err != nil {
		log.Fatal(err)
	}

	// 匹配的行数 修改的行数 更新的行数(数据不存在插入的行数) 更新的id
	fmt.Printf("matched:%d modified:%d upserted:%d upsertedID:%v",
		updateOneResult.MatchedCount,
		updateOneResult.ModifiedCount,
		updateOneResult.UpsertedCount,
		updateOneResult.UpsertedID,
	)
}

// 查询文档
func findDoc(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 配置排序
	findOpts := options.Find().SetSort(bson.M{"name": 1})

	// 查询条件
	findCursor, err := collection.Find(ctx, bson.M{"age": 18}, findOpts)

	// 查询条件
	var results []bson.M

	// 查询
	if err = findCursor.All(ctx, &results); err != nil {
		log.Fatal(err)
	}

	// 对结果进行循环输出
	for _, result := range results {
		fmt.Println(result)
	}
}

// 删除单个文档
func deleteDoc(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 进行删除
	deleteResult, err := collection.DeleteOne(ctx, bson.M{"name": "二狗"})

	// 错误判断
	if err != nil {
		log.Fatal(err)
	}

	// 输出已经删除的行数
	fmt.Println("delete count:", deleteResult.DeletedCount)
}

// 删除多个文档
func deleteManyDoc(client *mongo.Client, ctx context.Context) {
	// 连接到要操作的集合
	collection := client.Database("test").Collection("hero")

	// 删除
	deleteManyResult, err := collection.DeleteMany(ctx, bson.D{{"name", "李四"}})

	// 错误判断
	if err != nil {
		log.Fatal(err)
	}

	// 输出删除的条数
	fmt.Println("delete count", deleteManyResult.DeletedCount)
}

```