# Skill: ai-gateway-governance

- **名称**: `ai-gateway-governance`
- **描述**: 基于 Casbin 的 LLM 资源权限与治理逻辑。
- **触发词**: ["集成 Casbin", "模型权限管控", "RBAC 迁移", "配置治理策略"]

## 核心工作流

1. **权限建模**: 使用 Casbin 定义 `r = sub, obj, act`。`sub` 为身份标识，`obj` 为路径或模型 ID，`act` 为操作动作（如 `use`）。
2. **动态策略映射**: 将 API 业务逻辑（如 `allowed_models`）动态转换为 Casbin 内部策略，实现非持久化的快速生效。
3. **策略与逻辑解耦**: 将复杂的权限规则从 Go 代码中抽离到 `.conf` 文件中，支持通过配置动态调整管理权限。

## 决策启发式
- 当权限逻辑包含“且”、“或”或层级继承关系时，优先使用 Casbin。
- 模型访问权限应与 API Key 的 Scope 强绑定。
- 治理引擎在 Pre-request 阶段执行 Enforce，在 Post-request 阶段执行异步核算。

## 验收标准
- 跨租户、跨模型的非法访问被拦截。
- 权限策略变更无需重启服务即可生效。
