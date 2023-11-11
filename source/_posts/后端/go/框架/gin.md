---
title: go gin使用
tags: go
categories: 后端
date: 2023-11-11 15:10:03
---
<meta name="referrer" content="no-referrer"/>

## 基础

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

## 实现用户登录

### main.go

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func Login(c *gin.Context) {
	c.HTML(200, "login.html", nil)
}

func Welcome(c *gin.Context) {
	username := c.PostForm("username")
	password := c.PostForm("password")
	c.HTML(200, "welcome.html", gin.H{
		"username": username,
		"password": password,
	})
}

func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/*")
	r.GET("/login", Login)
	r.POST("/login", Welcome)
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

### login.html

> form 一定要把里面的input包起来，找了很久的问题！！！！！！！！！！

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div>
    <form action="/login" method="post">
        用户：<input type="text" name="username"><br>
        密码：<input type="text" name="password"><br>
        <input type="submit" value="登录">
    </form>
</div>
</body>
</html>
```

### welcome.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
welcome---{{.username}}
</body>
</html>
```

## 参数处理

```go
package main

import (
	"github.com/gin-gonic/gin"
)

// TestGet 取query参数
func TestGet(c *gin.Context) {
	// 取query里的参数  ---》 ?后面的
	s := c.Query("username")
	// 如果password没传，就设置默认参数
	pwd := c.DefaultQuery("password", "999")
	c.String(200, "username=%s,password=%s", s, pwd)
}

// TestGetParams 取params参数
func TestGetParams(c *gin.Context) {
	s := c.Param("name")
	s2 := c.Param("age")
	c.String(200, "name=%s,age=%s", s, s2)
}

// TestSearch form post 和 query
func TestSearch(c *gin.Context) {
	page := c.DefaultQuery("page", "0")
	key := c.PostForm("key")
	c.String(200, "page=%s,key=%s", page, key)
}

func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/*")
	r.GET("/login", TestGet)
	r.GET("/param/:name/:age", TestGetParams)
	r.POST("/search", TestSearch)
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

## 参数绑定到结构体

### test_form.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<form action="/register" method="post">
    名称：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    爱好：<input type="checkbox" name="hobby" value="swimming">游泳
    <input type="checkbox" name="hobby" value="basketball">篮球
    <br>
    性别：
    <input type="radio" name="gender" id="1" value="m">男
    <input type="radio" name="gender" id="2" value="f"> 女
    <br>
    城市：
    <select name="city">
        <option value="beijing">北京</option>
        <option value="shanghai">上海</option>
    </select>
    <br>
    <input type="submit" value="注册">
</form>
</body>
</html>
```

### main.go

```go
package main

import (
	"github.com/gin-gonic/gin"
)

type User struct {
	Username string   `form:"username"`
	Password string   `form:"password"`
	Hobby    []string `form:"hobby"`
	Gender   string   `form:"gender"`
	City     string   `form:"city"`
}

func GoRegister(c *gin.Context) {
	c.HTML(200, "test_form.html", nil)
}

// Register 绑定form post参数
func Register(c *gin.Context) {
	var user User
	c.ShouldBind(&user)
	c.String(200, "form data= %s", user)
}

// User2 绑定get参数，结构体也是使用也是form
type User2 struct {
	Username string `form:"username"`
	Password string `form:"password"`
}

// GetBindQuery http://localhost:8080/GetBindQuery?username=tom&password=99
func GetBindQuery(c *gin.Context) {
	var user User2
	c.ShouldBind(&user)
	c.String(200, "get bind query %s", user)
}

// User3 params绑定需要使用uri!!!!!!!!!!!!!!
type User3 struct {
	Username string `uri:"username"`
	Password string `uri:"password"`
}

// GetBindParams http://localhost:8080/GetBindParams/tom/66
// 绑定方法需要使用 c.ShouldBindUri
func GetBindParams(c *gin.Context) {
	var user User3
	c.ShouldBindUri(&user)
	c.String(200, "get bind params uri %s", user)
}

func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/*")
	r.GET("/register", GoRegister)
	r.POST("/register", Register)

	// get bind query
	r.GET("/GetBindQuery", GetBindQuery)

	// get bind params
	r.GET("/GetBindParams/:username/:password", GetBindParams)
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

## 使用静态文件

> 需要把bootstrap的css和js放到assets文件夹下

### main.go

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func GoStatic(c *gin.Context) {
	c.HTML(200, "test_static.html", nil)
}

func main() {
	r := gin.Default()
	// 第一个参数是使用的路径，第二个参数是实际路径（相对路径）
	r.Static("/assets", "./assets")
	r.LoadHTMLGlob("templates/*")

	r.GET("/GoStatic", GoStatic)
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

### test_static.html

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="/assets/css/bootstrap.min.css" type="text/css">
    <script src="/assets/js/bootstrap.min.js"></script>
</head>
<body>
<select class="form-select" aria-label="Default select example">
    <option selected>Open this select menu</option>
    <option value="1">One</option>
    <option value="2">Two</option>
    <option value="3">Three</option>
</select>
</body>
</html>
```

## 中间件

1. 使用`gin.Default()`，默认自带两个中间件`Logger(),Recovery()`
2. 使用`gin.New()`，不自带中间件

### 自定义全局中间件和路由中间件

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func Test(c *gin.Context) {
	c.String(200, "hello word")
}

func MyMiddleware(c *gin.Context) {
	fmt.Println("全局自定义中间件")
}

func MyMiddleware2(c *gin.Context) {
	fmt.Println("路由自定义中间件")
}

func main() {
	r := gin.Default()

	// 全局中间件
	// Logger 中间件将日志写入 gin.DefaultWriter，即使你将 GIN_MODE 设置为 release。
	// By default gin.DefaultWriter = os.Stdout
	//r.Use(gin.Logger())

	// Recovery 中间件会 recover 任何 panic。如果有 panic 的话，会写入 500。
	//r.Use(gin.Recovery())

	// 全局中间件
	r.Use(MyMiddleware)

	// 路由中间件
	r.GET("/Test", MyMiddleware2, Test)
	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

## BasicAuth 中间件

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)

// 模拟私人数据，只有在弹窗输入正确用户密码时才会返回
var secrets = gin.H{
	"foo": gin.H{
		"email": "foo@bar.com",
		"phone": "12345679811",
	},
}

func Handle(c *gin.Context) {
	fmt.Println(gin.AuthUserKey)
	// gin.AuthUserKey = 'user'
	// 是在输入后，gin存到上下文的相当于set("user","这里弹窗是输入的用户名")
	// MustGet 取到gin存入的数据
	user := c.MustGet(gin.AuthUserKey).(string)
	fmt.Println(user)
	// 这里设置登录进去返回的数据，从 secrets 里取
	if secret, ok := secrets[user]; ok {
		c.JSON(http.StatusOK, gin.H{"user": user, "secret": secret})
	} else {
		c.JSON(http.StatusOK, gin.H{"user": user, "secret": "no secret"})
	}
}

func main() {
	r := gin.Default()

	// 这里设置输入框输入的用户密码是否和 gin.Accounts 的对应。admin开头的接口都需要验证
	ra := r.Group("/admin", gin.BasicAuth(gin.Accounts{
		"foo": "foo",
		"baz": "baz",
	}))
	ra.GET("/secrets", Handle)

	r.Run() // 监听并在 0.0.0.0:8080 上启动服务
}

```

## cookies

```go
package main

import "github.com/gin-gonic/gin"

func Handle(c *gin.Context) {
	s, err := c.Cookie("username")
	if err != nil {
		s = "yw"
		// 键名，值，过期时间，路径（/ = 当前目录），域名，是否使用https（true是 false否），是否只允许http访问
		c.SetCookie("username", s, 60*60, "/", "localhost", false, true)
	}
	c.String(200, "设置cookies")
}

func main() {
	c := gin.Default()
	c.GET("/test_cookies", Handle)
	c.Run()
}

```

## 上传文件

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func Upload(c *gin.Context) {
	// 单文件上传
	file, _ := c.FormFile("file")
	fmt.Println(file.Filename)
	// 上传文件到项目根目录，使用原文件名
	//c.SaveUploadedFile(file, file.Filename)
	// 上传到指定文件夹
	c.SaveUploadedFile(file, "./files/"+file.Filename)
	c.String(200, "upload file filename=%s", file.Filename)
}

func GoUpload(c *gin.Context) {
	c.HTML(200, "test_upload.html", nil)
}

func main() {
	c := gin.Default()

	// 为multipart forms 设置较低的内存限制
	c.MaxMultipartMemory = 8 << 20
	c.LoadHTMLGlob("templates/*")
	c.GET("/upload", GoUpload)
	c.POST("/upload", Upload)
	c.Run()
}

```

## restful curd

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"strconv"
)

type User struct {
	UID  int    `json:"uid"`
	Name string `json:"name"`
	Age  int    `json:"age"`
}

var users = make([]User, 3)

func init() {
	u1 := User{1, "tom", 11}
	u2 := User{2, "kite", 44}
	u3 := User{3, "rose", 88}
	users = append(users, u1)
	users = append(users, u2)
	users = append(users, u3)
	fmt.Println(users)
}

// Find 模拟从数据库找数据
func Find(uid int) (*User, int) {
	// 循环，第一个是下标，第二个是用户数据
	for i, u := range users {
		// 如果用户数据和传入的id相等
		if u.UID == uid {
			// 返回找到用户的地址，下标
			return &u, i
		}
	}
	// 没找到用户数据为nil，下标为-1
	return nil, -1
}

// AddUser 增加
func AddUser(c *gin.Context) {
	u4 := User{
		UID:  4,
		Name: "joe",
		Age:  2222,
	}
	users = append(users, u4)
	c.JSON(200, users)
}

// DelUser 删除
func DelUser(c *gin.Context) {
	// 从params取到uid（字符串类型）
	uid := c.Param("uid")
	// 将字符串的uid转成int id
	id, _ := strconv.Atoi(uid)
	// 查找用户
	_, i := Find(id)
	// append users[:i]代表从下标0到i，user[i+1]: 代表从i+1到最后，...代表平铺
	users = append(users[:i], users[i+1:]...)
	c.JSON(200, users)
}

// UpdateUser 更新
func UpdateUser(c *gin.Context) {
	uid := c.Param("uid")
	id, _ := strconv.Atoi(uid)
	u, _ := Find(id)
	u.Name = "qq"
	fmt.Println(u)
	fmt.Println(*u)
	c.JSON(200, *u)
}

// FindUser 查找用户
func FindUser(c *gin.Context) {
	uid := c.Param("uid")
	id, _ := strconv.Atoi(uid)
	u, _ := Find(id)
	c.JSON(200, u)
}

func main() {
	c := gin.Default()

	c.GET("/user/:uid", FindUser)
	c.POST("/user/:uid", AddUser)
	c.PUT("/user/:uid", UpdateUser)
	c.DELETE("/user/:uid", DelUser)
	c.Run()
}

```