# 模块拆分与职责（更新版）

## 入口与启动
- `openclaw.mjs`: Node 入口，启用 compile cache，加载构建产物 `dist/entry.js`。
- `src/entry.ts`: CLI 进程级初始化（环境变量、终端色彩、ExperimentalWarning 处理、profile 参数）。
- `src/cli/run-main.ts`: CLI 主流程，路由短路、注册命令/插件、执行 Commander。
- `src/index.ts`: 导出公共 API，同时可作为主模块运行。

## CLI 与命令体系
- `src/cli/*`: CLI 基础设施（参数解析、help、预执行 hook、子 CLI）。
- `src/commands/*`: 具体命令实现（setup/onboard/config/status/health/message/agents 等）。

## 网关与服务编排（已验证）
- `src/gateway/server.impl.ts`: Gateway 总装配，负责配置校验、插件加载、通道管理、HTTP/WS、监控/维护、热重载与关闭流程。
- `src/gateway/server-http.ts`: HTTP 入口（hooks / tools / openai / openresponses / control ui / canvas host）。
- `src/gateway/server-ws-runtime.ts`: WS 连接管理与请求分发。
- `src/gateway/server-channels.ts`: 通道生命周期管理（start/stop/status）。
- `src/gateway/server-startup.ts`: sidecars（浏览器控制、Gmail hooks、内部 hooks、plugin services、channels 启动）。
- `src/gateway/boot.ts`: 启动时 BOOT.md 指令运行。

## 通道系统（已验证）
- `src/channels/registry.ts`: 核心通道元数据、顺序、文档路径与别名。
- `src/channels/dock.ts`: 轻量 docking 配置（能力、allowFrom、mention 规则、threading 等）。
- `src/channels/plugins/*`: 运行时通道插件注册（“重模块”）。

## 路由与会话（已验证）
- `src/routing/resolve-route.ts`: 绑定规则 -> agentId + sessionKey 路由。
- `src/routing/session-key.ts`: session key 规范（main/peer 等）。

## Agent 运行（已验证）
- `src/agents/pi-embedded-runner.ts`: 运行 API 对外汇总。
- `src/agents/pi-embedded-runner/run.ts`: 运行入口，负责模型/认证选择、失败重试、上下文保护、结果汇总。
- `src/agents/pi-embedded-runner/run/attempt.ts`: 单次运行尝试，构建系统提示词、加载工具/skills、会话管理、订阅流式输出。

## 插件与扩展（推断）
- `src/plugins/*`: 插件加载、服务与 hook 运行。
- `extensions/*`: 外部扩展包（可添加新通道/功能）。

## 基础设施
- `src/infra/*`: 运行时/平台/环境/路径/端口。
- `src/process/*`: 子进程与队列。
- `src/logging*`: 日志与控制台捕获。
- `src/terminal/*`: 终端输出与交互。
- `src/security/*`: 安全相关。

