---
layout: '../../layouts/MarkdownPost.astro'
title: 'Golang Web 学习'
pubDate: 2022-04-18
description: 'Go web、Gin 学习'
author: 'Aaron'
cover:
    url: 'https://lookcos.cn/usr/uploads/2022/04/2067928922.png'
    square: 'https://lookcos.cn/usr/uploads/2022/04/2067928922.png'
    alt: 'cover'
tags: ["golang", "Web"]
theme: 'light'
featured: false
---

# 来自表单的 Post 请求

数据以 name-value 对的形式，通过 POST 请求发送。
数据内容再 POST 请求的 Body 里。
通过 POST 发送的 name-value 数据对的格式通过表单的 Content Type 来指定，也就是 enctype 属性：默认值 `application/x-www-form-urlencoded`, 除此之外浏览器还需要支持 `multipart/form-data`; HTML 5 的话还需要支持 text/plain 。

如果 enctype 是`application/x-www-form-urlencoded`，浏览器会将表单数据编码到查询字符串里，如：`first_name=sau%20sheong&last_name=chang`.

如果 enctype 是`multipart/form-data`，那么每一个 name-value 对都会被转换成一个MIME消息部分，每一个部分都有自己的 Content Type 和 Content Disposition


# Gin
```bash
go mod init go_learning
go get -u github.com/gin-gonic/gin
```

```go
package main

import (
  "net/http"

  "github.com/gin-gonic/gin"
)

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
      "message": "pong",
    })
  })
  r.GET("/news", func(c *gin.Context) {
	c.String(200, "Hello news!")
  })
  r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```

## gin 模板基本语法
1. {{.}} 输出数据
2. 变量 `{{$t := .title}}`
3. 比较函数 `gt ge lt`
4. 循环遍历：
```html
<ul>
{{range $key, $value:=.hobby}}
  <li>{{$key}} --- {{$value}}</li>
{{else}}
  <li>数组中没有数据</li>
{{end}}
</ul>
```
5. With

before:
```html
<h4>{{.user.Name}}</h4>
<h4>{{.user.Gender}}</h4>
<h4>{{.user.Age}}</h4>
```
now:
```html
{{with .user}}
<h4>{{.Name}}</h4>
<h4>{{.Gender}}</h4>
<h4>{{.Age}}</h4>
{{end}}
```

6. 自定义模板函数：放在加载模板之前（LoadHTMLGlobal）

```go
r.SetFuncMap(template.FuncMap{
		"UnixToTime":UnixToTime,
})
```

7. 嵌套template

8. 静态文件服务
```go
// 配置静态web目录，第一个参数表示路由，第二个参数表示映射的目录
r.Static("/aaa", "./static")
// static目录下一般放css, images, js
```

9. Get 请求传值
```go
username := c.Query("username")
page := c.DefaultQueryj("page", "1")
```

10. Post 请求传值获取form表单数据
```go
r.POST("/doAddUser", func(c *gin.Context) {
  username := c.PostForm("username")
  password := c.PostForm("password")
  age := c.DefaultPostFormj("age", "20")

  c.JSON(http.StatusOK, gin.H{
    "username": username,
    "password": password,
  })
})
```

11. 获取GET POST 传递的数据绑定到结构体 
c.ShouldBind(&user)

12. 路由分组 路由文件分离
```go
xxxRouters := r.Grop("/admin") {
  xxxRouters.GET("/", func(c *gin.Context) {
    c.String(200, "后台首页")
  })
}
```

13. 自定义控制器controller 及 控制器的继承

14. Gin 中间件：开发者在处理请求的过程中加入用户自己的钩子（Hook）函数。如登录认证、权限校验、数据分页、记录日志、耗时统计等。
r.GET可以加入不止一个回调func。
c.Next() 调用该请求的剩余处理程序
r.Use() 全局中间件

15. 自定义model

16. 文件上传

17. cookie & session