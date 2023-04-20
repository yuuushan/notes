# Gin 处理请求参数

## 路径参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/item/:id", func(c *gin.Context) {
		id := c.Param("id")

		c.JSON(200, gin.H{
			"id": id,
		})
	})
	r.Run()
}
```

测试请求如下：

```bash
curl -X GET 'http://127.0.0.1:8080/item/1'
## 输出：{"id":"1"}
```

## 查询参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.POST("/post", func(c *gin.Context) {
		id := c.Query("id")
		page := c.DefaultQuery("page", "0")

		c.JSON(200, gin.H{
			"id":      id,
			"page":    page,
		})
	})
	r.Run()
}
```

测试请求如下：

```bash
curl -X POST 'http://127.0.0.1:8080/post?id=1'
## 输出：{"id":"1","page":"0"}

curl -X POST 'http://127.0.0.1:8080/post?id=2&page=1'
## 输出：{"id":"2","page":"1"}
```

## Post form

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.POST("/post", func(c *gin.Context) {
		id := c.PostForm("id")
		page := c.PostForm("page")

		c.JSON(200, gin.H{
			"id":      id,
			"page":    page,
		})
	})
	r.Run()
}
```

测试请求如下：

```bash
curl -X POST 'http://127.0.0.1:8080/post' -F 'id=1' -F 'page=2'
## 输出：{"id":"1","page":"2"}
```
