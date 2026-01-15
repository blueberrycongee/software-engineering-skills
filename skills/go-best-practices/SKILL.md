---
name: go-best-practices
description: Go language development best practices and project layout standard; use when drafting or reviewing Go projects, code style, error handling, concurrency, performance, or tooling setup.
---

# Go 语言开发最佳实践规范 (v1.0)

遵循核心哲学: 清晰优于聪明 (Clarity > Cleverness)。
黄金法则: 使用 gofmt 后的代码就是标准格式。

## 1. 项目结构 (Project Layout)

遵循 Standard Go Project Layout。

- `/cmd`: 主应用入口。每个子目录是一个可执行程序 (如 `/cmd/server`, `/cmd/worker`)。这里只写启动逻辑, 不写业务逻辑。
- `/internal`: 私有应用代码。此路径对外部项目不可导入, 用于保护项目封闭性。
- `/pkg`: 可复用库代码 (可选)。需要被外部项目引用时放这里; 仅内部使用则放 `/internal`。
- `/api`: 协议定义, 如 OpenAPI/Swagger、Proto 文件等。

## 2. 代码风格与格式 (Style & Formatting)

### 2.1 格式化 (Mandatory)

- 强制使用 `gofmt` 或 `goimports` (推荐, 自动管理 import) 进行保存时自动格式化。
- Imports 分组顺序:
  1) 标准库 (如 `fmt`, `os`)
  2) 第三方库 (如 `github.com/gin-gonic/gin`)
  3) 本项目库 (如 `myproject/internal/...`)

### 2.2 命名规范

- 包名: 简短、单数、全小写, 避免下划线。
  - Bad: `user_service`, `MyUtil`
  - Good: `user`, `util`, `http`
- 接口:
  - 单方法接口使用 `er` 结尾, 如 `Reader`, `Writer`, `Formatter`。
- 变量长度:
  - 作用域越小, 名字越短。
  - Good: 小循环用 `i`。
  - Good: 参数用 `ctx` (Context), `req` (Request)。
  - Good: 全局变量或长函数使用描述性命名, 如 `userLastLoginTime`。
- Getter/Setter:
  - 避免 `Get` 前缀。
  - Bad: `user.GetName()`
  - Good: `user.Name()` (获取), `user.SetName()` (设置)

## 3. 错误处理 (Error Handling)

### 3.1 检查每一个 error

- 严禁使用 `_` 忽略 error。

```go
// Bad
_ = json.Unmarshal(data, &v)

// Good
if err := json.Unmarshal(data, &v); err != nil {
    return fmt.Errorf("unmarshal failed: %w", err)
}
```

### 3.2 错误包装 (Wrapping)

- 向上层返回错误时, 必须添加当前层面的上下文信息。
- 使用 `%w` 进行错误包装。

```go
// Bad
return err

// Good
return fmt.Errorf("reading user %d config failed: %w", userID, err)
```

### 3.3 Panic 策略

- 仅在启动阶段遇到不可恢复错误时使用 `panic` (如配置缺失、数据库不可达)。
- 业务逻辑中严禁 `panic`, 必须返回 `error`。

## 4. 并发最佳实践 (Concurrency)

### 4.1 上下文传递 (Context Propagation)

- 所有涉及 I/O、耗时操作的函数, 第一个参数必须是 `ctx context.Context`。
- 目的: 允许上游取消调用链 (超时控制、优雅退出)。

### 4.2 Goroutine 生命周期管理

- 永远不要启动一个你不知道何时结束的 Goroutine。
- 使用 `sync.WaitGroup` 等待任务完成, 或通过 `context` 通知退出, 防止泄漏。

### 4.3 Channel vs Mutex

- 口诀: Share memory by communicating, don't communicate by sharing memory.
- 传递数据/信号: 使用 `chan`。
- 保护内部状态 (缓存 Map、计数器): 优先 `sync.Mutex` 或 `sync.RWMutex`。

## 5. 性能与优化 (Performance)

### 5.1 Slice 初始化

- 已知长度时, 使用 capacity 初始化以避免多次扩容。

```go
// Bad
var list []User
for _, u := range users {
    list = append(list, u)
}

// Good
list := make([]User, 0, len(users))
```

### 5.2 接口设计 (Interface Design)

- 原则: 接收接口, 返回结构体 (Accept interfaces, return structs)。
- 参数尽量用接口类型 (如 `io.Reader`), 返回值尽量用具体类型 (如 `*File`)。

## 6. 工具链配置 (Tooling)

### 6.1 Linter 配置

- 推荐使用 `golangci-lint`。
- 核心检查项建议包含:
  - `govet`
  - `errcheck`
  - `gocyclo`
  - `staticcheck`

### 6.2 Makefile

- 在项目根目录提供 Makefile, 统一操作指令:

```makefile
.PHONY: lint test build

lint:
	golangci-lint run

test:
	go test -race ./...

build:
	go build -o bin/server ./cmd/server
```

## 自检清单

- 格式: 是否运行了 `gofmt`/`goimports`?
- 错误: 所有 `err` 是否都处理并带上下文包装?
- 并发: Goroutine 是否可退出? `go test -race` 是否通过?
- 接口: 接口是否足够小?
- 注释: 导出函数是否有注释?
