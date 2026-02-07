# 核心代码解读（更新版）

## Gateway 核心
### `src/gateway/server.impl.ts`
- Gateway 的总装配入口：配置校验/迁移、插件加载、HTTP/WS 启动、通道管理、维护任务、热重载与关闭流程。
- 关键子流程：
  - 配置合法性检查 + legacy 迁移。
  - 载入插件与 channel methods（插件可扩展 gateway 方法集合）。
  - 构建 runtime state（HTTP server、WS server、canvas host、auth、hooks 等）。
  - ChannelManager 管理所有通道生命周期。
  - 附加 WS handlers，暴露 gateway methods 与事件广播。
  - 启动 sidecars（浏览器控制、hooks、plugin services、channels）。
  - config reloader 支持热重载/重启。

### `src/gateway/server-http.ts`
- HTTP 入口路由（hooks / tools / slack / openai / openresponses / control ui / canvas host）。
- Canvas/Control UI 访问受 auth 或已授权 WS 连接保护。

### `src/gateway/server-channels.ts`
- 通道生命周期管理：
  - `startChannel` 读取 config，逐 account 启动。
  - 处理 enabled/configured 状态，更新 runtime snapshot。
  - `stopChannel` 触发 abort + 可选 stopAccount。

## Channels 核心
### `src/channels/registry.ts`
- 维护核心通道列表、展示信息、文档路径/label。
- 提供通道别名与标准化能力。

### `src/channels/dock.ts`
- 轻量 docking 配置：
  - 通道能力（chatTypes、polls、reactions、threads 等）。
  - allowFrom 解析/格式化。
  - mention 规则、threading 规则、replyTo 逻辑。
- 用于共享代码路径，避免引入重插件实现。

### `src/channels/plugins/index.ts`
- 从运行时插件注册表读取通道插件，去重并按顺序排序。
- 提供 `listChannelPlugins/getChannelPlugin` 等接口。

## Routing 核心
### `src/routing/resolve-route.ts`
- 根据 bindings（channel/account/peer/guild/team）路由到 agentId 和 sessionKey。
- 默认规则: 先 peer -> parent peer -> guild/team -> account -> channel -> 默认 agent。

## Agent 核心
### `src/agents/pi-embedded-runner/run.ts`
- Agent 运行入口：负责模型/认证选择、failover、上下文窗口校验、自动 compaction、生成最终 payload。
- 特征：
  - auth profiles 轮转与 cooldown。
  - context overflow 时自动 compaction 重试。
  - 多种错误类型分类与用户友好提示。

### `src/agents/pi-embedded-runner/run/attempt.ts`
- 单次运行尝试：构建 tools/skills/sandbox、系统提示词、加载/修复 session、订阅流式输出。
- 特征：
  - 支持 hooks(before_agent_start/agent_end)。
  - 自动扫描 prompt 图片并注入历史图片。
  - session 文件修复与写锁。
  - 统一的工具/消息输出订阅机制。

## 待深入
- `src/gateway/server-methods/*` 与具体 WS 方法实现
- `src/channels/plugins/<channel>` 具体适配实现
- `src/agents/pi-tools.ts` 与 tool schema 设计
- `src/plugins/*` 插件服务与 hook 系统

