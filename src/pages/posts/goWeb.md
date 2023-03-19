---
layout: '../../layouts/MarkdownPost.astro'
title: 'Golang Web 学习'
pubDate: 2022-04-18
description: '很多Go web 学习'
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