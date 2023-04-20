# Gin 快速上手

## 第一个 gin 项目

创建一个 go 项目：

```bash
mkdir gindemo; cd gindemo
go mod init gindemo
```

新建一个 `main.go` 文件，内容如下：

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

执行 `go mod tidy` 来安装缺失的依赖，然后执行 `go run main.go`。默认监听 8080 端口。此时可以使用浏览器访问 "http://127.0.0.1:8080/ping" 或使用 curl 测试：

```bash
curl -X GET 'http://localhost:8080/ping
## 输出：{"message":"pong"}
```

## 修改端口号

如果你的 8080 端口已被占用，程序会报错。可以通过以下方式修改端口号：

```go
r.Run(":8088")
```

默认监听 `0.0.0.0`，如果希望只本机访问，可以改为：

```go
r.Run("127.0.0.1:8088")
```
