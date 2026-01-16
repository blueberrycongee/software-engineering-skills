# Skill: go-stability-hardening

- **名称**: `go-stability-hardening`
- **描述**: 针对 Go 后端项目的稳定性与并发安全加固方案。
- **触发词**: ["修复 Data Race", "解决内存泄露", "添加 Panic 恢复", "代码审计"]

## 核心工作流

1. **并发安全 (Deep Clone)**: 
    - **场景**: 当内存 Store 返回包含 Map/Slice 的结构体时。
    - **规范**: 必须为结构体实现 `Clone()` 方法，对引用类型字段进行显式 `make` 和循环赋值，禁止返回浅拷贝。
2. **资源管理 (Context Safety)**:
    - **规范**: 凡是使用 `context.WithCancel` 创建的子 Context，必须在方法退出前（含 defer 路径）调用 `cancel()`。
3. **防御性编程 (Global Recovery)**:
    - **规范**: 在 Web 处理链的最外层挂载 `RecoveryMiddleware`，捕获所有 `panic` 并记录结构化日志，返回标准 500 JSON。

## 决策启发式
- 任何返回内存中长期持有对象的 Get 操作都必须考虑 Clone。
- 流式处理（Streaming）和异步任务必须严格检查 Context 生命周期。
- 编译不通过或测试报错时，优先检查接口签名与 Mock 实现的一致性。

## 验收标准
- 开启 `go test -race` 全量通过。
- 压力测试下协程数与内存占用保持稳定。
