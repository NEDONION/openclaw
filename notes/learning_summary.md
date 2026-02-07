# 学习总结（更新版）

## 已确认的关键事实
- 项目是 TypeScript/Node 的 CLI + Gateway 体系，支持多渠道消息接入与插件扩展。
- Gateway 通过 HTTP/WS 暴露能力，包含 hooks、OpenAI/OpenResponses 兼容接口、控制 UI、canvas host。
- 通道系统分为轻量 docking 配置（共享逻辑）与重插件实现（运行时加载）。
- Agent 运行基于 pi-* 体系：负责模型选择、认证轮换、系统提示词构建、会话管理、工具注入与流式订阅。

## 设计思路总结
- “轻重分离”：通道 docking 与插件实现分离，避免共享代码路径引入重依赖。
- “可扩展”：Gateway methods + plugin services + channel plugins 形成扩展面。
- “鲁棒性”：配置迁移、运行期热重载、auth profile 轮转、context overflow 自动 compaction。

## 值得借鉴
- Gateway 启动流程中统一装配（配置校验、插件加载、channel manager、sidecars）。
- Agent run 过程中对错误类型的精细分类与用户友好提示。
- hooks 与 plugin services 的可插拔结构。

## 潜在优化点（待验证）
- 进一步梳理 server-methods 的职责划分与调用链。
- 为多通道适配补充统一的可观测性指标（启动/断开/错误）。
- 对 channel plugins 的运行时热替换/重载策略进行评估。

