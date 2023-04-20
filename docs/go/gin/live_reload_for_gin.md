# Gin 热重载

热重载可以让我们不用每次修改代码都重新执行 `go run main.go`。

## 使用 nodemon 实现热重载

Gin 没有提供这样的方法，但是可以借助第三方工具来实现。

这里选择比较活跃的开源工具 [nodemon](https://github.com/remy/nodemon) 来实现 gin 的热重载。

首先安装 nodemon：

```bash
npm install -g nodemon # or using yarn: yarn global add nodemon
```

然后使用 nodemon 启动 gin 程序：

```bash
nodemon --exec go run main.go --signal SIGTERM
```

## 编写 Makefile

虽然使用 nodemon 实现热重载很方便，但是每次都要输这么长的命令很难记忆。我们可以在项目根目录下创建一个 `Makefile` 文件：

```makefile
.PHONY: run
run:
    @nodemon --exec go run main.go --signal SIGTERM
```

然后在 `Makefile` 所在目录执行 `make run` 即可。
