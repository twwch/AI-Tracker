# 为什么你的"AI-First"战略可能是错的——一位前 Meta LLaMA 工程师的实战复盘

![封面](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-strategy-cover.jpg)

> 原文作者：Peter Pang（CreaoAI 联合创始人，前 Meta GenAI / LLaMA 团队，前 Apple）
> 原文链接：[X/Twitter](https://x.com/intuitiveml/status/2043545596699750791)
> 互动数据：3.4K 赞 / 518 转发 / 9.7K 收藏 / 176 万浏览

---

## 一句话总结

**99% 的生产代码由 AI 编写。** 25 人团队，10 名工程师，日均 3-8 次生产部署。他们不是给现有流程"加上 AI"，而是把整个工程体系拆掉重建。

---

## "AI 辅助"和"AI-First"的本质区别

大多数公司做的事：工程师打开 Cursor，PM 用 ChatGPT 写 PRD，QA 试用 AI 测试生成。流程没变，效率提升 10-20%。

**这叫 AI 辅助（AI-assisted），不是 AI-First。**

![AI-First 不等于使用 AI](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-ai-first-vs-assisted.jpg)

AI-First 意味着：围绕"AI 是主要构建者"这一假设，重新设计流程、架构和组织。不再问"AI 怎么帮工程师？"，而是问"怎么重构一切让 AI 来建造，工程师提供方向和判断？"

Peter 观察到一个普遍问题：很多团队自称 AI-First，却还在跑同样的 Sprint 周期、同样的 Jira 看板、同样的周会和 QA 签核。他们只是把 AI 加进了循环，没有重新设计循环。

---

## 三个致命瓶颈

### 1. 产品管理瓶颈

PM 花数周研究、设计、写规格文档。但 Agent 两小时就能实现一个功能。**当构建时间从数月坍塌到数小时，数周的规划周期就变成了约束。**

> 花几个月想一个东西，然后两小时就造出来——这不合理。

### 2. QA 瓶颈

Agent 两小时交付功能，QA 花三天测边界条件。解法：用 AI 构建测试平台来测试 AI 写的代码。验证必须和实现同速。

### 3. 人力瓶颈

竞对 100 倍人力做类似的事。25 人团队不可能靠招人追平，只能靠重新设计流程。

---

## 大胆决策：统一架构

旧架构分散在多个独立系统，改一个功能要动三四个仓库。人类工程师尚可管理，**AI Agent 完全看不到全貌**——无法推理跨服务影响，无法本地跑集成测试。

解法：**全部合入单体仓库（Monorepo）**。原因只有一个——让 AI 能看到一切。

![统一架构：让 AI 看到全貌](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-unified-architecture.jpg)

> 这就是 Harness Engineering 原则的实践：把越多的系统拉进 Agent 可检查、可验证、可修改的形式，你获得的杠杆就越大。

Peter 花一周设计新系统，再花一周用 Agent 重构整个代码库。CreaoAI 是一个 Agent 平台——他们用自己的 Agent 来重建运行 Agent 的平台。

---

## 技术栈详解

| 层级 | 工具 | 作用 |
|------|------|------|
| 基础设施 | AWS | 自动扩缩容 + 熔断回滚 |
| 可观测性 | CloudWatch | 结构化日志，25+ 告警，每日自动查询 |
| CI/CD | GitHub Actions | 6 阶段流水线：CI → 构建部署 Dev → 测试 Dev → 部署 Prod → 测试 Prod → 发布 |
| AI 代码审查 | Claude Opus 4.6 | 每个 PR 触发 3 路并行审查：代码质量 / 安全漏洞 / 依赖供应链 |
| Feature Flag | Statsig | 灰度发布 + A/B 测试 + 即时 Kill Switch |
| PR 管理 | Graphite | Merge Queue + Stacked PR |
| 错误追踪 | Sentry + CloudWatch | 跨工具上下文聚合 |
| 任务管理 | Linear | 自动创建工单 + 去重 + 自动关闭 |

### 自愈反馈闭环（核心亮点）

每天早上 9:00 UTC，自动健康检查流程启动：

1. **Claude Sonnet 4.6** 查询 CloudWatch，分析全服务错误模式，生成健康报告推送到 Teams
2. 一小时后，**分诊引擎**启动：聚类生产错误，按 9 个维度评分严重性，自动在 Linear 创建调查工单
3. 系统自动去重：已有 Issue 则更新，已关闭 Issue 复发则检测回归并重新打开
4. 工程师推送修复 → 同一套审查/CI/部署流水线 → 分诊引擎重新检查 → 错误消失则自动关闭工单

**检测 → 分诊 → 修复 → 验证，最小化人工干预。**

![自愈反馈闭环](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-self-healing-loop.png)

---

## 功能从想法到生产的完整路径

### 新功能路径

1. 架构师定义结构化 Prompt（代码库上下文 + 目标 + 约束）
2. Agent 分解任务、规划实现、写代码、生成测试
3. 开 PR → 3 路 Claude 审查 + 人类审查（审战略风险，不逐行看代码）
4. CI 验证：类型检查、Lint、单元测试、集成测试、E2E 测试
5. Graphite Merge Queue rebase + 重跑 CI + 合入
6. 6 阶段部署流水线 → Feature Flag 灰度 → 监控指标
7. 指标异常 → Kill Switch / 熔断自动回滚

### Bug 修复路径

1. CloudWatch + Sentry 检测错误
2. Claude 分诊引擎评分 + 创建 Linear 工单（含完整调查上下文）
3. 工程师验证 + 推送修复 → 同一套流水线
4. 分诊引擎重新验证 → 解决则自动关闭

**两条路径，同一套系统，同一个标准。**

![功能从想法到生产的路径](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-feature-flow.jpg)

---

## 成果

- 14 天内日均 **3-8 次生产部署**（旧模式下同期甚至无法完成一次发布）
- 当天构思的新功能 **当天上线**
- 有问题的功能 **当天下线**
- A/B 测试实时验证效果
- 用户参与度 **上升**，支付转化率 **上升**

![成果数据](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/ai-first-results.jpg)

> 人们以为我们在用速度换质量。事实恰恰相反——反馈循环越紧，学得越快。日更比月更学到的东西多得多。

---

## 新型工程组织

### 架构师（1-2 人）

- 设计 SOP，教 AI 如何工作
- 构建测试基础设施、集成系统、分诊系统
- 决定架构和系统边界
- **批判 AI，而不是跟随 AI**

> 我有物理学博士学位。博士教给我最有用的能力是质疑假设、压力测试论证、发现缺失。批判 AI 的能力比产出代码的能力更有价值。

### 操作员（其他人）

AI 给人类分配任务。分诊系统发现 Bug → 创建工单 → 给出诊断 → 分配给合适的人。人类调查、验证、审批。**AI 提 PR，人类审风险。**

### 谁适应最快？

**初级工程师比资深工程师适应更快。** 初级工程师没有十年习惯要推翻，工具放大了他们的影响力。资深工程师最艰难——两个月的工作被 AI 一小时完成，多年积累的稀有技能突然贬值。

> 在这个转型中，适应力比积累的技能更重要。

---

## 管理的变化

- 两个月前，60% 的时间用于管人。现在不到 10%。
- 从管理者回归构建者：每天 9 AM 到 3 AM 写代码、设计 SOP、维护 Harness
- 团队关系反而更好——不再争论技术决策，有了更多非工作话题的交流
- 原则：不会因为 AI 引入生产 Bug 就开除工程师，而是改进审查流程、加强测试、增加护栏

---

## 超越工程

如果工程团队几小时交付功能，但市场部要一周才能发公告——**市场部就是瓶颈**。

CreaoAI 将 AI 原生运营推入每个职能：

- 产品发布说明：AI 从 Changelog 自动生成
- 功能介绍视频：AI 生成动效
- 社交媒体日更：AI 编排 + 自动发布
- 健康报告和分析摘要：AI 从 CloudWatch 和数据库自动生成

**工程、产品、市场、增长在同一个 AI 原生工作流中运行。**

---

## 对行业的启示

**对工程师：** 你的价值正在从代码产出转向决策质量。写代码快的价值每月递减，评估、批判和指导 AI 的能力每月递增。产品审美（taste）很重要。

**对 CTO 和创始人：**
- PM 流程比构建时间长？先改 PM 流程
- 先建测试 Harness，再扩 Agent——没有快速验证的快速 AI 就是快速技术债
- 从一个架构师开始，证明系统可行后再扩展
- 把 AI 原生推入每个职能
- 预期阻力，有些人会 push back

**对行业：** OpenAI、Anthropic 和多个独立团队殊途同归——结构化上下文、专业化 Agent、持久化记忆、执行循环。Harness Engineering 正在成为标准。

> 我相信一人公司将变得普遍。如果一个架构师加上 Agent 能完成 100 人的工作，很多公司不需要第二个员工。

---

> 原文作者：[Peter Pang (@intuitiveml)](https://x.com/intuitiveml) — CreaoAI 联合创始人，前 Meta GenAI (LLaMA)，前 Apple
> 项目：[creao.ai](https://creao.ai/)
