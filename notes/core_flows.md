# 核心流程（初版）

> 标注“推断”的流程来自目录结构与命名推理，需在深入阅读后校准。

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

## 2) 命令注册与路由（已验证）
来源: `src/cli/program/*`, `src/cli/program/command-registry.ts`

```mermaid
flowchart LR
  A[buildProgram] --> B[createProgramContext]
  A --> C[configureProgramHelp]
  A --> D[registerPreActionHooks]
  A --> E[registerProgramCommands]
  E --> F[src/commands/*]
  E --> G[CLI subcommands]
```

## 3) 消息处理主链路（推断）
来源: 目录结构 `src/gateway`, `src/routing`, `src/channels`, `src/agents`, `src/providers`。

```mermaid
flowchart LR
  A[Channel Adapter] --> B[Gateway Server]
  B --> C[Routing]
  C --> D[Agent Runtime]
  D --> E[Model Provider]
  E --> D
  D --> C
  C --> B
  B --> A
```

## 4) 配置/会话流程（推断）
来源: `src/config/*`, `src/sessions/*`, `src/commands/*`。

```mermaid
sequenceDiagram
  participant CLI as CLI
  participant Config as Config
  participant Sessions as Sessions
  CLI->>Config: loadConfig()
  CLI->>Sessions: loadSessionStore()
  CLI->>Sessions: saveSessionStore()
```

