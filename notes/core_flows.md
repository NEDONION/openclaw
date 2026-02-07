# 核心流程（更新版）

## 1) CLI 启动流程（已验证）
来源: `openclaw.mjs` -> `src/entry.ts` -> `src/cli/run-main.ts`

```mermaid
sequenceDiagram
  participant User as 用户
  participant MJS as openclaw.mjs
  participant Entry as src/entry.ts
  participant CLI as src/cli/run-main.ts
  participant Program as Commander
  User->>MJS: 运行 openclaw
  MJS->>Entry: import dist/entry.js
  Entry->>Entry: 规范化环境/参数
  Entry->>Entry: 必要时重启进程(屏蔽ExperimentalWarning)
  Entry->>CLI: import runCli()
  CLI->>CLI: 读 dotenv/normalize env/路径修正
  CLI->>CLI: tryRouteCli(短路命令)
  CLI->>Program: buildProgram/注册命令
  CLI->>Program: parseAsync(argv)
```

## 2) Gateway 启动流程（已验证）
来源: `src/gateway/server.impl.ts` + `src/gateway/server-startup.ts`

```mermaid
flowchart TD
  A[startGatewayServer] --> B[校验/迁移配置]
  B --> C[加载插件 + channel methods]
  C --> D[resolveGatewayRuntimeConfig]
  D --> E[createGatewayRuntimeState]
  E --> F[NodeRegistry/Subscriptions]
  F --> G[startDiscovery + tailscale]
  G --> H[startMaintenanceTimers]
  H --> I[attach WS handlers]
  I --> J[start HTTP server]
  J --> K[start sidecars]
  K --> L[start config reloader]
  L --> M[ready]
```

## 3) Gateway HTTP 请求处理（已验证）
来源: `src/gateway/server-http.ts`

```mermaid
flowchart TD
  A[HTTP request] --> B{Hooks?}
  B -->|yes| H[handleHooks]
  B -->|no| C{Tools invoke?}
  C -->|yes| T[handleTools]
  C -->|no| D{Slack?}
  D -->|yes| S[handleSlack]
  D -->|no| E{OpenResponses?}
  E -->|yes| OR[handleOpenResponses]
  E -->|no| F{OpenAI chat?}
  F -->|yes| OA[handleOpenAI]
  F -->|no| G{Canvas/Control UI?}
  G -->|yes| UI[serve UI/Canvas]
  G -->|no| Z[404]
```

## 4) Channel 生命周期（已验证）
来源: `src/gateway/server-channels.ts`

```mermaid
sequenceDiagram
  participant GW as Gateway
  participant CM as ChannelManager
  participant Plugin as Channel Plugin
  GW->>CM: startChannels()
  CM->>Plugin: listAccountIds
  CM->>Plugin: startAccount(account)
  Plugin-->>CM: runtime status updates
  GW->>CM: stopChannel(channel, account)
  CM->>Plugin: stopAccount(optional)
```

## 5) 路由与 session key（已验证）
来源: `src/routing/resolve-route.ts`

```mermaid
flowchart TD
  A[resolveAgentRoute] --> B[过滤 binding: channel + accountId]
  B --> C{peer match?}
  C -->|yes| R1[use binding.peer]
  C -->|no| D{parent peer?}
  D -->|yes| R2[use binding.peer.parent]
  D -->|no| E{guild/team match?}
  E -->|yes| R3[use binding.guild/team]
  E -->|no| F{account or channel binding?}
  F -->|yes| R4[use binding.account/channel]
  F -->|no| R5[default agent]
  R1 --> G[build sessionKey]
  R2 --> G
  R3 --> G
  R4 --> G
  R5 --> G
```

## 6) Agent 单次运行（已验证）
来源: `src/agents/pi-embedded-runner/run.ts` + `run/attempt.ts`

```mermaid
flowchart TD
  A[runEmbeddedPiAgent] --> B[选择 provider/model + auth profile]
  B --> C[上下文窗口校验]
  C --> D[runEmbeddedAttempt]
  D --> E[构建 tools/skills/sandbox]
  E --> F[构建 system prompt]
  F --> G[加载/修复 session]
  G --> H[订阅流式输出]
  H --> I[prompt + 处理 images]
  I --> J[回收结果/错误]
  J --> K{需 failover/compaction?}
  K -->|yes| B
  K -->|no| L[返回 payloads + meta]
```

