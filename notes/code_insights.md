# 核心代码解读（初版）

> 只记录已阅读文件的高置信结论，后续会补充详细函数解读与难点分析。

## 已读核心文件
1) `openclaw.mjs`
- Node 启动入口，启用 compile cache，然后加载构建产物 `dist/entry.js`。

2) `src/entry.ts`
- 负责 CLI 进程级初始化（环境变量、色彩输出、ExperimentalWarning 处理）。
- 如未禁用，会重启 Node 进程并注入 `--disable-warning=ExperimentalWarning`。
- 解析 CLI profile 参数并调用 `runCli()`。

3) `src/cli/run-main.ts`
- CLI 主入口：读取 env、运行时校验、执行路由短路、注册命令与插件。
- 通过 `Commander` 解析并执行命令。

4) `src/cli/program/*`
- `buildProgram()` 组织 help、preaction、命令注册等。
- `command-registry.ts` 定义命令注册与部分命令路由短路逻辑。

## 待深入的关键区域（下一步）
- `src/gateway/*`: 网关主流程、HTTP/WS、会话、通道编排
- `src/channels/*` 与各渠道实现: 连接/事件/消息映射
- `src/agents/*` + `src/providers/*`: agent 执行与模型调用
- `src/plugins/*` + `extensions/*`: 插件系统与扩展加载
- `src/config/*`, `src/sessions/*`: 配置与会话管理

