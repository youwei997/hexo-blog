---
title: go gorm基础
tags: go
categories: 后端
date: 2023-11-11 15:11:01
---
<meta name="referrer" content="no-referrer"/>

> https://gorm.io/docs/index.html

## 连接

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

func main() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(db)
}

```

## 创建表

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	fmt.Println("init函数自动调用，连接数据库")
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
}

// 创建表
func createTable() {
	db.AutoMigrate(&User{})
}

func main() {
	createTable()
}

```

## 创建

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	fmt.Println("init函数自动调用，连接数据库")
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
}

// 创建表
func createTable() {
	db.AutoMigrate(&User{})
}

var user = User{
	Name:     "tom",
	Age:      12,
	Birthday: time.Now(),
}

// 插入一条
func insertOne() {
	result := db.Create(&user)
	fmt.Println("result.RowsAffected", result.RowsAffected)
	fmt.Println("user.ID", user.ID)
}

// 指定字段插入
func insertSelect() {
	db.Select("Name", "Age", "CreateAt").Create(&user)
}

// 批量插入
func insertMany() {
	var users = []User{
		{Name: "John", Age: 12, Birthday: time.Now()},
		{Name: "Bob", Age: 17, Birthday: time.Now()},
		{Name: "Zs", Age: 14, Birthday: time.Now()}}
	db.Create(&users)
}

// 插入map类型
func insertMap() {
	db.Model(&User{}).Create(map[string]interface{}{
		"Name": "Bob", "Age": 12,
	})
}
func (u *User) BeforeCreate(tx *gorm.DB) (err error) {
	fmt.Println("BeforeCreate 钩子，每次插入数据之前执行")
	return
}
func main() {
	//createTable()
	//insertOne()
	//insertSelect()
	//insertMany()
	insertMap()
}


```

## 查询

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	fmt.Println("init函数自动调用，连接数据库")
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
}

// 创建表
func createTable() {
	db.AutoMigrate(&User{})
}

//var user = User{
//	Name:     "tom",
//	Age:      12,
//	Birthday: time.Now(),
//}

func selectOne() {
	var user User

	// 默认找第一条
	//db.First(&user)
	//// SELECT * FROM users ORDER BY id LIMIT 1;
	//fmt.Println(user.ID)

	// 找最后一条（需要注释上面的查询）
	db.Last(&user)
	// SELECT * FROM users ORDER BY id DESC LIMIT 1;
	fmt.Println(user.ID)
}

// 使用id查询
func findById() {
	//var user User
	// SELECT * FROM users WHERE id = 10;
	//db.First(&user, 4)
	//fmt.Println(user)

	var users []User
	db.Find(&users, []int{1, 2, 3})
	// SELECT * FROM users WHERE id IN (1,2,3);
	for _, user := range users {
		fmt.Println(user.ID)
	}
}

// 查询所有
func findAll() {
	var users []User
	db.Find(&users)
	// SELECT * FROM users;
	for _, user := range users {
		fmt.Println(user.ID)
	}
}

func main() {
	//selectOne()
	//findById()
	findAll()
}

```

## 更新

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	fmt.Println("init函数自动调用，连接数据库")
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
	Active   bool
}

// 创建表
func createTable() {
	db.AutoMigrate(&User{})
}

// 插入数据
func insert() {
	user := User{
		Name:     "tom",
		Age:      10,
		Birthday: time.Now(),
		Active:   false,
	}
	db.Create(&user)
	fmt.Println(user)
}

// 更新第一条数据
func update() {
	var user User
	db.First(&user) // id == 1
	user.Name = "qq"
	user.Age = 99
	db.Save(&user)
}

// 根据条件更新
func update2() {
	// 所有用户的名字都改为lisi
	//db.Model(&User{}).Update("name", "lisi")

	// 将指定的用户（根据主键）修改名字
	//var user User
	//db.First(&user, 1)
	//db.Model(&user).Update("name", "hello")

	// 根据条件修改
	db.Model(&User{}).Where("active=?", false).Update("name", "")
}

// 使用结构体更新
func update3() {
	var user User
	db.First(&user)
	db.Model(&user).Updates(User{Name: "王五", Age: 0, Active: true})
}

func main() {
	//createTable()
	//insert()
	//update()
	//update2()
	update3()
}

```

## 删除

```go
package main

import (
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	fmt.Println("init函数自动调用，连接数据库")
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
	Active   bool
}

// 创建表
func createTable() {
	db.AutoMigrate(&User{})
}

func insert() {
	user := User{
		Name:     "zs",
		Age:      11,
		Birthday: time.Now(),
		Active:   true,
	}
	db.Create(&user)
}

// 删除数据（先查再删）
func delete1() {
	var user User
	db.First(&user)
	db.Delete(&user) // 软删除
}

// 根据id删除
func delete2() {
	db.Delete(&User{}, 2)
}

// 查找软删除记录（相当于显示包含软删除的全部数据，正常情况软删除后的数据，不会再被查询出来）
func unscoped() {
	var users []User
	db.Unscoped().Where("name=?", "zs").Find(&users)
	for _, user := range users {
		fmt.Println(user.ID)
	}
}

func main() {
	//delete1()
	//insert()
	//delete2()
	unscoped()
}

```

## 原生sql

```go
package main

import (
	"database/sql"
	"fmt"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
	"time"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// User 表结构
type User struct {
	gorm.Model
	// 自定义自动全部需要大写
	Name     string
	Age      int
	Birthday time.Time
	Active   bool
}

// 通过原生sql查询
func sqlRaw1() {
	type Result struct {
		ID   int
		Name string
		Age  int
	}
	var result Result
	db.Raw("select id,name,age from users where name=?", "zs").Scan(&result)
	fmt.Println(result)
}

// 通过exec
func sqlRaw2() {
	db.Exec("update users set age=? where name=?", "100", "王五")
}

// sql命名参数
func sqlRaw3() {
	var user User
	// sql.Named 第一个name指的是@myname!!!!!!!!!!
	db.Where("name=@myname", sql.Named("myname", "zs1")).Find(&user)
	fmt.Println(user)
}

func main() {
	//sqlRaw1()
	//sqlRaw2()
	sqlRaw3()
}

```

## Belongs To (many to one)

> 多个用户属于一个企业

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

func createTable1() {
	// many to one
	type Company struct {
		ID   int
		Name string
	}

	type User struct {
		gorm.Model
		Name string
		// 默认外键ID 必须是模型+ID
		CompanyID int
		Company   Company
	}

	db.AutoMigrate(&Company{}, &User{})
}

func createTable2() {
	type Company struct {
		ID   int
		Name string
	}

	type User struct {
		gorm.Model
		Name         string
		CompanyRefer int
		// 自定义外键名称
		Company Company `gorm:"foreignKey:CompanyRefer"`
		// use CompanyRefer as foreign key
	}
	db.AutoMigrate(&Company{}, &User{})
}

func main() {
	createTable2()
}

```

## Has One (one to one)

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

// one to one
func createTable1() {
	// User has one CreditCard, UserID is the foreign key
	type CreditCard struct {
		gorm.Model
		Number string
		UserID uint
	}
	type User struct {
		gorm.Model
		CreditCard CreditCard
	}

	db.AutoMigrate(&User{}, &CreditCard{})
}

// 多态
func createTable2() {

	type Toy struct {
		ID        int
		Name      string
		OwnerID   int    // 表主键
		OwnerType string // 表名
	}
	type Cat struct {
		ID   int
		Name string
		Toy  Toy `gorm:"polymorphic:Owner;"`
	}

	type Dog struct {
		ID   int
		Name string
		Toy  Toy `gorm:"polymorphic:Owner;"`
	}

	db.AutoMigrate(&Toy{}, &Dog{}, &Cat{})

	db.Create(&Dog{Name: "dog1", Toy: Toy{Name: "toy1"}})
	// INSERT INTO `dogs` (`name`) VALUES ("dog1")
	// INSERT INTO `toys` (`name`,`owner_id`,`owner_type`) VALUES ("toy1","1","dogs")

}

func main() {
	//createTable1()
	createTable2()
}

```

## Has Many (one to many)

> 一个用户拥有多个信用卡

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

type User struct {
	gorm.Model
	CreditCards []CreditCard
}

type CreditCard struct {
	gorm.Model
	Number string
	UserID uint
}

// one to many
func createTable1() {
	db.AutoMigrate(&User{}, &CreditCard{})
}

func main() {
	createTable1()
}

```

## Many To Many

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"log"
)

var db *gorm.DB

// 连接数据库(init函数自动调用)
func init() {
	// refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
	dsn := "root:123456@tcp(127.0.0.1:3306)/go_gorm?charset=utf8mb4&parseTime=True&loc=Local"
	d, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
	db = d
}

type User struct {
	gorm.Model
	Languages []Language `gorm:"many2many:user_languages;"`
}

type Language struct {
	gorm.Model
	Name string
}

// many to many
func createTable1() {
	db.AutoMigrate(&User{}, &Language{})
}

func insert() {
	l := Language{
		Name: "chinese",
	}

	l2 := Language{
		Name: "english",
	}
	user := User{
		Languages: []Language{l, l2},
	}
	// 只插入user数据，上面关联的 l,l2 也会在 languages 表自动插入。并且中间表 user_languages 也会插入对应的数据
	db.Create(&user)
}

func main() {
	//createTable1()
	insert()
}

```

## 实体关联

> https://gorm.io/docs/associations.html

## session

> https://gorm.io/docs/session.html

## 事务

- 事务要么都成功，要么都失败。有一个失败就回滚

> https://gorm.io/docs/transactions.html

### 跳过事务

```go
package main

func main() {
	// Globally disable
	db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
		SkipDefaultTransaction: true,
	})

	// Continuous session mode
	tx := db.Session(&Session{SkipDefaultTransaction: true})
	tx.First(&user, 1)
	tx.Find(&users)
	tx.Model(&user).Update("Age", 18)
}

```