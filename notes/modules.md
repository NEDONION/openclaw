# 模块拆分与职责（初版）

> 本文基于目录结构与已阅读文件整理；职责将在后续阅读中细化与校准。

## 入口与启动
- `openclaw.mjs`: Node 入口，启用编译缓存并加载构建后的 `dist/entry.js`。
- `src/entry.ts`: CLI 运行入口，处理参数、环境初始化、必要时重启进程以屏蔽 ExperimentalWarning。
- `src/cli/run-main.ts`: CLI 主流程，路由命令、注册插件命令、解析并执行 Commander 指令。
- `src/index.ts`: 导出公共 API，且支持作为主模块直接执行。

## CLI 与命令体系
- `src/cli/*`: CLI 基础设施（参数解析、help、配置、预执行 hook、子 CLI）。
- `src/commands/*`: 具体命令实现（setup/onboard/config/status/health/message/agents 等）。

## 网关与路由（推断）
- `src/gateway/*`: 网关核心（HTTP/WS、会话、鉴权、插件、通道服务编排）。
- `src/routing/*`: 消息路由与通道选择逻辑。
- `src/sessions/*`: 会话与状态管理。

## 渠道与适配层（推断）
- `src/channels/*`: 通道通用能力/抽象。
- `src/telegram`, `src/discord`, `src/slack`, `src/signal`, `src/imessage`, `src/web`, `src/whatsapp`, `src/line`: 各平台适配。

## 代理/模型/记忆（推断）
- `src/agents/*`: agent 运行与编排。
- `src/providers/*`: LLM/外部模型与服务提供方适配。
- `src/memory/*`: 记忆/检索/存储。
- `src/tts/*`: 语音/文本处理。

## 媒体与内容管线（推断）
- `src/media/*`: 媒体处理。
- `src/link-understanding`, `src/media-understanding`: 内容理解/解析。

## 插件与扩展（推断）
- `src/plugins/*`: 插件加载、插件命令、运行时桥接。
- `extensions/*`: 独立扩展包（如 msteams/matrix 等）。

## 基础设施
- `src/infra/*`: 运行时/平台/环境/路径/端口。
- `src/process/*`: 子进程与执行。
- `src/logging*`: 日志与控制台捕获。
- `src/terminal/*`: 终端输出与交互。
- `src/security/*`: 安全相关（推断）。

