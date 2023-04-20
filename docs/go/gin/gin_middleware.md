# Gin 使用中间件

## 处理请求时传入多个 handler

`r.GET()` 接收两个参数，第一个是路由相对路径，第二个是 `handlers ...gin.HandlerFunc`。在之前的代码中，我们都会传入一个函数来处理请求。其实我们也可以传入多个函数：

```go
package main

import (
	"fmt"

	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.GET(
		"/ping",
		func(c *gin.Context) {
			fmt.Println("handler 1")
			c.JSON(200, gin.H{
				"message": "pong",
			})
		},
		func(c *gin.Context) {
			fmt.Println("handler 2")
		},
	)
	r.Run()
}
```

执行之后，当我们请求 `:8080/ping` 时，程序会先打印 "handler 1"，然后打印 "handler 2"。说明 gin 是按顺序执行这两个 handler。

那么如果我们有一些公共的 handler，会在很多的请求中使用。那么，我们就可以把它定义成中间件。

## 定义一个简单的中间件

这里我们假设有一个需求，在每一个请求进来的时候，在终端打印当前时间。这里我们就可以定义一个中间件：

```go
package main

import (
	"fmt"
	"time"

	"github.com/gin-gonic/gin"
)

func Timer() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now())
		fmt.Println(t.Format("2006-01-02 15:04:05 Mon")
	}
}

func main() {
	r := gin.New()
	r.Use(Timer())

	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run()
}
```

在之前的代码中，我们会使用 `r := gin.Default()` 来构建 `*gin.Engine`。这实际上会默认绑定 `Logger` 和 `Recovery` 中间件，来帮助我们打印日志和处理 `panic`。

如果不希望使用默认的中间件，可以使用 `r := gin.New()` 来代替 `r := gin.Default()`。

`gin.Default()` [定义](https://github.com/gin-gonic/gin/blob/master/gin.go#L202-L208)如下：

```go
// Default returns an Engine instance with the Logger and Recovery middleware already attached.
func Default() *Engine {
	debugPrintWARNINGDefault()
	engine := New()
	engine.Use(Logger(), Recovery())
	return engine
}
```

我们上面的代码中只使用了我们自定义的 `Timer` 中间件。执行程序，这时我们在处理每个请求前都会打印当前的时间。

## 使用中间件实现更复杂的需求

在上面的中间件中，请求的处理顺序是先执行 `Timer`，再执行 `r.GET` 时传入的 `handler`。那么如果我们想在请求执行完成后再获取一次时间，以计算请求处理总时长，该怎么做？如果我在 `Timer` 中间件生成的数据，希望传给后面的 `handler`，又该怎么做？

```go
package main

import (
	"fmt"
	"time"

	"github.com/gin-gonic/gin"
)

func Timer() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now()

		c.Set("startTime", t)
		fmt.Println(t.Format("2006-01-02 15:04:05 Mon"))

		c.Next()

		fmt.Println(time.Since(t))
	}
}

func main() {
	r := gin.New()
	r.Use(Timer())

	r.GET("/ping", func(c *gin.Context) {
		t := c.MustGet("startTime")
		c.JSON(200, gin.H{
			"message": "pong",
			"time":    t,
		})
	})
	r.Run(":8081")
}
```

上面的代码，会在请求开始时打印当前时间，并在请求结束时打印请求处理所消耗的时间。使用 `c.Next`，程序会先执行后面的 handlers，在全部执行完成后，再继续执行 `c.Next` 后的内容。

使用 `c.Set`，可以将中间件中的一些数据保存起来以供后面的 handlers 使用。在后面的 handlers 中，我们可以使用 `c.MustGet` 来讲数据取出，如果数据不存在则报错。如果数据不一定存在的话，可以使用 `c.Get`。
