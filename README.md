# 🚀 AI Tracker — AI 前沿动态追踪

本仓库追踪 AI 领域最新动态，推荐优秀的 AI 工具、Skills、开源项目，分享大模型前沿资讯与实战教程。

---

## 📰 最新推文

### 2026-04-17

- [HyperFrames: HeyGen 开源的 AI-First 视频渲染框架](20260417/HyperFrames-HeyGen开源HTML视频渲染框架/content.md) — 用 HTML 写视频，让 AI Agent 做导演
- [Claude Opus 4.7 重磅发布](20260417/Claude-Opus-4.7重磅发布-编码能力飙升Mythos之影笼罩/content.md) — 编码能力飙升，Mythos 之影笼罩

### 2026-04-16

- [Claude 身份认证升级后的备选总览](20260416/00-Claude身份认证升级后的备选总览-合并版/content.md)
- [Claude Opus 4.7 爆料 — 新设计工具或本周登场](20260416/Claude-Opus-4.7爆料-新设计工具或本周登场/content.md)
- [Modal 免费 GLM-5.1 接入 Claude Code 超详细部署教程](20260416/Modal免费GLM-5.1接入Claude-Code超详细部署教程/content.md)
- [NVIDIA NIM 免费 MiniMax-M2.7 接入 Claude Code 超详细部署教程](20260416/NVIDIA-NIM免费MiniMax-M2.7接入Claude-Code超详细部署教程/content.md)
- [阿里 ATH 事业群发布首款 AI 开发工具 Meoo](20260416/阿里ATH事业群发布首款AI开发工具Meoo/content.md)

---

## 🧠 模块一：LLM 底层逻辑与 Token 经济学
**核心目标：** 理解 LLM 的运行“内存”限制与预测本质。

* **核心视频:** [Intro to Large Language Models - Andrej Karpathy](https://www.youtube.com/watch?v=zjkBMFhNj_g)
* **核心笔记:**
    * **Token 原子论**: 理解 Tokenization 对拼写、算术的影响。
    * **上下文窗口即 RAM**: LLM 的 Context Window 决定了其处理复杂任务的上限。
    * **预测机制**: 理解“预测下一个 Token”如何导致上下文腐烂（Context Decay）。
    * **工作记忆限制**: 警惕 "Lost in the Middle" 效应，优化信息在 Prompt 中的排布。

---

## 🛠️ 模块二：从提示词工程进化到上下文工程
**核心目标：** 学习如何通过优化 Token 配置来提升模型效能。

* **核心视频:** [Context Engineering & MCP Intro](https://www.youtube.com/watch?v=yjBUnbRgiNs)
* **上下文四大策略:**
    | 策略 | 说明 | 场景 |
    | :--- | :--- | :--- |
    | **写入 (Write)** | 将详情存入外部存储（如文件、数据库） | 持久化知识 |
    | **选择 (Select)** | 仅加载当前步骤所需的“高信号”信息 | 节省 Token 消耗 |
    | **压缩 (Compress)** | 对冗余信息进行摘要或状态持久化 | 处理长文本 |
    | **隔离 (Isolate)** | 通过子代理架构减少 Context 污染 | 复杂多任务并行 |
* **核心原则:** 遵循 Anthropic 的 **“金锁区 (Goldilocks Zone)”** 原则，保持 Prompt 长度恰到好处。

---

## 🔌 模块三：MCP 协议深挖与实战搭建
**核心目标：** 打通 LLM 与本地/云端数据的“连接层”。

* **核心视频:**
    * [MCP Tutorial & Setup](https://www.youtube.com/watch?v=zrs_HWkZS5w)
    * [Model Context Protocol in Action](https://www.youtube.com/watch?v=yjBUnbRgiNs)
* **技术要点:**
    * **架构理解**: 客户端-服务器模型（Client-Server Model）。
    * **核心三要素**: 工具 (Tools)、资源 (Resources)、提示词 (Prompts)。
    * **环境搭建**:
        * 使用 `Filesystem` MCP 读写本地文件。
        * 使用 `Google Drive / GitHub` MCP 访问远程资产。
    * **调试技巧**: 使用 **MCP Inspector** 监控调用生命周期。

---

## ⚡ 模块四：Claude Skills 与 Agent 专家构建
**核心目标：** 实现具备“专家经验”的持久化代理，掌握三层加载机制。

* **核心视频:**
    * [Building Claude Skills - Official Guide](https://www.youtube.com/watch?v=QNiaoD5RxPA)
    * [Deep Dive into Claude Desktop Skills](https://www.youtube.com/watch?v=yDc0_8emz7M)
* **渐进式披露 (Progressive Disclosure) 架构:**
    1.  **元数据层 (Metadata)**: 始终在线的轻量目录。
    2.  **指令层 (Instructions)**: 仅在匹配需求后才加载的详细规则。
    3.  **资源层 (Resources)**: 按需调用的脚本 (Scripts) 与参考文件。
* **项目级持久化**: 利用 `CLAUDE.md` 记录项目上下文、编码标准与状态。

---

## 🚀 推荐学习工具
* **编辑器**: [Cursor](https://www.cursor.com/) 或 [VS Code](https://code.visualstudio.com/)
* **终端**: [Claude Code](https://anthropic.com/claude-code) (官方 CLI 工具)
* **客户端**: [Claude Desktop](https://claude.ai/download) (支持 MCP 配置)
* **社区**: [Linux.do](https://linux.do/) — 高质量中文技术社区，AI / 开源 / 开发者话题活跃

---
