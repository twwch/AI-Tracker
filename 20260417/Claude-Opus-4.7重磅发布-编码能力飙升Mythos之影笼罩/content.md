# Claude Opus 4.7 重磅发布：编码能力飙升，但 Mythos 之影笼罩

![Cover](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/cover-opus47.png)

> 2026 年 4 月 16 日，Anthropic 正式发布 Claude Opus 4.7 —— 目前最强的公开可用（GA）大模型。编码基准大幅领先，视觉能力 3 倍提升，但 Anthropic 罕见地公开承认：它不如自家未发布的 Mythos。

![LLM Stats Banner](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/llmstats-banner.png)

---

## 一句话总结

**Claude Opus 4.7 在编码和 Agent 任务上拿下多项 SOTA，但 Anthropic 主动承认它不敌未公开的 Claude Mythos Preview，这在 AI 竞赛史上极为罕见。**

---

## 核心升级一览

![Claude Opus 4.7 Key Upgrades](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/features-opus47.png)

### 1. 编码能力大幅跃升

这是 Opus 4.7 最大的亮点：

| 基准测试 | Opus 4.7 | Opus 4.6 | 提升幅度 |
|----------|----------|----------|---------|
| **SWE-bench Pro** | 64.3% | 53.4% | **+20%** |
| **SWE-bench Verified** | 87.6% | 80.8% | **+8.4%** |
| **CursorBench** | 70% | 58% | **+12pp** |
| **Terminal-Bench 2.0** | 69.4% | 65.4% | +6.1% |

在 Rakuten-SWE-Bench 上，Opus 4.7 解决的生产级任务数量是 4.6 的 **3 倍**。多步 Agent 推理能力提升 14%，工具调用错误减少 2/3。

### 2. 高分辨率视觉（3x 提升）

这是 Claude 家族首次支持高分辨率图像处理：

- 最大分辨率从 **1,568px（~1.15MP）→ 2,576px（~3.75MP）**
- 视觉精度评分达 **98.5%**
- 坐标映射现在与实际像素 1:1 对应
- 适用场景：扫描合同、技术图纸、财务报表等企业文档分析

### 3. 全新 `xhigh` 推理等级

在原有的 `high` 和 `max` 之间新增 `xhigh` 等级，让开发者在推理深度和延迟之间拥有更精细的控制。特别适合：
- 复杂 Bug 调试
- 架构设计决策
- 安全分析

下图展示了不同 effort level 下 Opus 4.7 vs 4.6 的编码性能对比，可以看到 **xhigh 在 Token 消耗和性能之间取得了最佳平衡**：

![Agentic Coding Performance by Effort Level](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/opus47-tokens-official.png)

### 4. Task Budgets（公测中）

面向 API 用户的新功能 —— 为 Agent 循环设定 Token 预算，模型会自动分配资源优先级并优雅收尾。

### 5. 自我验证能力

Opus 4.7 会在输出前 **主动验证自己的结果**，减少需要人工复查的场景。Anthropic 官方描述："你可以放心把最难的工作交给它，减少监督。"

### 6. Claude Code 新命令：`/ultrareview`

全新代码审查命令，深度分析架构、安全性、性能和可维护性 —— "能发现一个认真审稿人会发现的问题"。

---

## 基准跑分横评

以下是 Anthropic 官方发布的完整基准测试对比表（含 Mythos Preview）：

![Official Benchmark Table](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/opus47-bench-official.png)

我们也制作了更直观的柱状图对比：

![Benchmark Comparison](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/benchmark.png)

### 与竞品的直接对比

| 基准测试 | Opus 4.7 | GPT-5.4 | Gemini 3.1 Pro |
|----------|----------|---------|----------------|
| **SWE-bench Pro** | **64.3%** | 57.7% | 54.2% |
| **GPQA Diamond** | 94.2% | **94.4%** | 94.3% |

在编码和 Agent 类任务上，**Opus 4.7 全面领先** GPT-5.4 和 Gemini 3.1 Pro。但在 GPQA Diamond（研究生级推理）上，三家前沿模型基本打平（94.2%-94.4%）。

---

## 房间里的大象：Claude Mythos

这次发布最引人注目的不是 Opus 4.7 本身 —— 而是 Anthropic **公开承认它不如自家未发布的 Mythos Preview**。

关键对比：
- **CyberGym 基准**：Mythos 83.1% vs Opus 4.7 73.1%
- Mythos 仅对少数经过审核的科技和网络安全公司开放
- Anthropic 在训练时 **主动削弱了** Opus 4.7 的网络安全能力
- 新增自动检测和阻止高风险网络安全请求的防护机制

Gizmodo 毫不客气地评价：**"Anthropic 发布 Opus 4.7，主要是为了提醒大家 Mythos 有多强。"**

Anthropic 目前没有公开发布 Mythos 的计划，但表示正在研究如何安全地部署 Mythos 级别的模型。

---

## 定价与可用性

- **定价不变**：输入 $5 / 百万 Token，输出 $25 / 百万 Token
- **上下文窗口**：100 万 Token（1M），无长上下文加价
- **最大输出**：128K Token
- **模型 ID**：`claude-opus-4-7`

### 可用平台

| 平台 | 状态 |
|------|------|
| Claude.ai / Claude 移动端 | ✅ 已上线 |
| Anthropic API | ✅ 已上线 |
| Amazon Bedrock | ✅ 已上线 |
| Google Cloud Vertex AI | ✅ 已上线 |
| Microsoft Foundry | ✅ 已上线 |
| GitHub Copilot | ✅ 已上线 |
| Microsoft 365 Copilot | ✅ 已上线 |

### 注意：新 Tokenizer

Opus 4.7 使用了新的 Tokenizer，同样的输入文本可能产生 **1.0-1.35 倍** 的 Token 数。虽然单价没变，但实际使用成本可能略有上升。

![Cloud Routines 介绍](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/usage-limits-opus47.png)
*Cloud Routines —— 定义可重复执行的例行流程，处理待办事项、审查 PR、响应云端事件*

---

## 同步发布：Claude Code 重大更新

与 Opus 4.7 一同发布的还有 Claude Code 的重要更新：

![Claude Code 全新桌面界面](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/claude-code-ui.png)
*Felix Rieseberg（Anthropic）发推展示全新 Claude Code 桌面应用 —— 从底层重构，专为并行工作设计*

- **并行处理**：单窗口内运行多个 Claude 实例，全新侧边栏统一管理
- **集成 IDE**：内置终端、文件编辑器、Diff 查看器
- **Cloud Routines**：定时任务 / API 触发 / GitHub 触发的自动化（"合上笔记本也能干活的云端员工"）
  - Pro 用户：5 次/天
  - Max 用户：15 次/天
- **Adaptive Thinking**：移动端上 Opus 4.7 采用"按需思考"模式，替代之前的"扩展思考"

![Claude 官方发推：Claude Code 桌面端重新设计](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/dev-workflow-opus47.png)
*Claude 官方宣布桌面端 Claude Code 重新设计，支持多会话并行*

---

## 社区反馈

### 正面评价

> "Opus 4.7 更有 Agent 感、更精准，在长时间运行的工作上好得多。它能跨会话保持上下文，处理歧义的能力也强多了。"
> —— Boris Cherny (@bcherny)

> "这是一个我非常喜欢在 Claude Code 中使用的模型。它更有 Agent 特质，指令遵循更好，同时又非常聪明和有创造力。"
> —— Thariq (@trq212)

CodeRabbit 的评测显示，Opus 4.7 在代码审查中捕获已知问题的能力提升了 **24%**（55→68/100）。

### 负面声音

- **Token 消耗过快**：有用户反映单次会话耗尽全部配额，模型以"修 Bug"为名重写了整个代码库
- **新 Tokenizer 变相涨价**：同样的输入多消耗 0-35% 的 Token
- **Mythos 焦虑**：社区对 Anthropic 手握更强模型却不公开发布感到不满
- **"AI 缩水"争议**：部分开发者认为这次发布是对 Opus 4.6 质量下降投诉的回应

---

## 总结

Claude Opus 4.7 是一次 **实打实的升级** —— 尤其在编码和 Agent 任务方面。SWE-bench Pro 64.3% 的成绩领先 GPT-5.4 和 Gemini 3.1 Pro，高分辨率视觉和自我验证能力进一步拓展了应用边界。

但这次发布也留下了一个意味深长的注脚：**Anthropic 的最强模型不在这里**。Mythos 的存在既展示了 Anthropic 的技术实力，也揭示了 AI 安全与能力之间日益尖锐的张力。

对开发者来说，Opus 4.7 已经是目前公开可用的最强编码 AI。但对整个行业来说，真正的故事才刚刚开始。

---

*信息来源：Anthropic 官方博客、CNBC、Axios、The Next Web、VentureBeat、Gizmodo、9to5Google、GitHub Blog、AWS Blog、Hacker News、36氪、X/Twitter*

*发布日期：2026-04-17*
