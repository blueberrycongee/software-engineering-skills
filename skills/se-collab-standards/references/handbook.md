# 软件工程多人协作与开发规范手册 (v1.0)

核心目标：降低认知成本、减少合并冲突、保证代码长期可维护。  
执行原则：工具约束 > 流程约束 > 人工约束。

## 1. 协作工作流 (Workflow)

### 1.1 分支管理 (Branching Model)

采用 Git Flow 或 GitHub Flow 模式。

- `main` / `master`：生产分支。永远保持稳定，随时可发布。严禁直接 Commit。
- `develop`：开发主轴。所有新功能合并至此，用于测试环境部署。
- `feature/*`：功能分支。从 `develop` 切出，格式：`feature/功能名` (如 `feature/login-page`)。
- `hotfix/*`：紧急修复。从 `main` 切出，修复后同时合并回 `main` 和 `develop`。

### 1.2 提交规范 (Commit Message)

遵循 Conventional Commits 标准。

格式：`<Type>(<Scope>): <Subject>`

Type 枚举：

- `feat`: 新功能
- `fix`: 修复 Bug
- `docs`: 仅文档变更
- `style`: 格式调整 (空格, 分号等)
- `refactor`: 代码重构 (不改变逻辑)
- `chore`: 构建工具/依赖更新

示例：`feat(auth): 增加微信登录接口`

### 1.3 合并请求 (Pull/Merge Request)

- 粒度：一个 PR 尽量只做一件事，代码变动建议控制在 400 行以内。
- 前置条件：必须通过 CI 自动化测试 + 至少 1 人 Code Review 通过。
- 合并策略：推荐使用 Squash Merge（将多次琐碎提交合并为一次清晰的提交记录）。

## 2. 代码编写规范 (Coding Standards)

### 2.1 命名规范

- 变量/函数：小驼峰 camelCase (如 `getUserInfo`)。
- 禁止：`temp`, `a`, `data` 等无意义命名。
- 要求：布尔值用 `is` / `has` / `can` 开头 (如 `canEdit`)。
- 类/接口/组件：大驼峰 PascalCase (如 `UserController`)。
- 常量：全大写下划线 UPPER_SNAKE_CASE (如 `MAX_RETRY_COUNT`)。
- 私有变量（视语言而定）：通常以 `_` 开头 (如 `_internalCache`)。

### 2.2 注释规范

核心原则：解释 Why (为什么这么做)，而不是 What (代码在做什么)。

文档注释：所有公共接口 (Public Methods/Classes) 必须包含参数、返回值、异常说明。

特殊标记：

- `// TODO`: 待办事项 (上线前尽量清理)。
- `// FIXME`: 这里的代码需要修复/优化。
- `// HACK`: 妥协的临时方案，需解释原因。

### 2.3 格式化 (Formatting)

强制工具化：不靠人力检查空格和换行。

- 前端：ESLint + Prettier
- 后端：Checkstyle (Java), Pylint/Black (Python), gofmt (Go)

提交拦截：配置 Git Hook (如 Husky)，格式检查不通过无法 Commit。

## 3. 架构与设计原则 (Design Principles)

### 3.1 SOLID 原则 (重点)

- 单一职责 (SRP)：一个类/函数只做一件事。如果有 "And" 逻辑，请拆分。
- 开闭原则 (OCP)：对扩展开放，对修改关闭。新增功能应通过“加代码”而非“改代码”实现（多用接口/多态，少用 if-else）。

### 3.2 结构质量

- DRY (Don't Repeat Yourself)：相同逻辑出现两次，请封装成函数。
- 高内聚低耦合：模块间尽量通过接口交互，禁止直接依赖具体实现类。
- 童子军法则：修改代码时，顺手清理在附近看到的烂代码（重命名变量、提取函数）。

## 4. 技术专项规范 (Technical Specs)

### 4.1 API 接口规范

设计风格：RESTful。

- `GET /users/1` (查)
- `POST /users` (增)
- `PUT /users/1` (改)
- `DELETE /users/1` (删)

统一响应体：

```json
{
  "code": 200,
  "message": "Success",
  "data": { ... },
  "trace_id": "ab123"
}
```

### 4.2 数据库设计 (Database)

- 必备字段：每张表必须包含 `id` (主键), `created_at` (创建时间), `updated_at` (更新时间)。
- 删除策略：核心业务数据严禁物理删除，使用 `is_deleted` (TinyInt/Boolean) 进行软删除。

数据类型：

- 金额：使用 Decimal 或 BigInt (存分)，严禁使用 Float/Double。
- 状态：尽量使用 TinyInt 配合枚举代码，或有明确语义的 String。

### 4.3 日志与异常 (Logging & Error)

- 严禁：使用 `System.out.println` 或 `console.log`。必须使用日志框架。

日志分级：

- `ERROR`: 系统故障，需要人工介入 (如 DB 连接失败)。
- `WARN`: 参数错误、逻辑校验失败。
- `INFO`: 关键业务流转 (如 "订单创建成功 ID:1001")。

异常处理：Fail Fast (快速失败)。在入口处校验参数，不符合直接抛异常，不要带着脏数据往下跑。

## 5. 代码审查清单 (Code Review Checklist)

在 Review 他人代码时，请对照以下清单：

- 功能：是否满足需求？边界条件（空值、负数、越界）是否处理？
- 设计：是否有重复代码？是否违反单一职责？
- 安全：是否有 SQL 注入风险？是否有敏感信息（密码/Key）硬编码？
- 性能：是否有循环内查询数据库？是否有大对象未释放？
- 可读性：命名是否清晰？是否需要补充注释？
- 测试：是否有对应的单元测试？

## 6. 工具推荐 (Tooling)

- IDE 插件：SonarLint (实时代码质量检查), GitLens (Git 增强)。
- API 文档：Swagger / Postman / YApi。
- CI/CD：GitLab CI / Jenkins / GitHub Actions (自动化构建与测试)。
