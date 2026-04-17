# Claude 4.7 发布了吗？还没。但整条时间线已经被社区挖得底朝天

> **一句话答案**：截至 **2026-04-16**（北京时间今天下午），**Claude Opus 4.7 仍未官方发布**。但爆料已经走到了"全网等一个按钮"的阶段——The Information 独家、Pankaj Kumar 推特 46 万浏览、Polymarket 砸到 **99% 概率** 6 月 30 日前发出。

![Claude Opus 4.7 从泄漏到发布的 17 天时间线](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/timeline-opus47.png)

---

## TL;DR · 3 分钟看懂整件事

| 维度 | 现状 |
| --- | --- |
| **发布状态** | 未官宣（今天是 The Information 报道的"本周"第 3 天） |
| **核心爆料源** | The Information · 2026-04-14 晚间独家（付费墙） |
| **最早苗头** | 2026-03-31 Claude Code 的 npm 包误发 **512K 行源码**，硬编码里出现 `opus-4-7`、`sonnet-4-8`、`mythos` |
| **随机彩蛋** | 与 Opus 4.7 一起登场的将是 **Anthropic 的 AI 设计工具**（网站/落地页/PPT 一句话生成） |
| **Polymarket 赔率** | **99%** 概率 Claude 4.7 在 6/30 前发布 · 总成交额 $87K |
| **市场反应** | 设计/建站股已被预期性锤了一轮：**FIG -6%、WIX -5%、ADBE -2.7%、GDDY -3%** |
| **旁观者** | OpenAI 对线发了 **GPT-5.4 Cyber**（防御性安全特化模型），对标 Claude Mythos |

---

## 1. 来龙去脉：17 天，5 个关键节点

### 🟦 Mar 31 · 源头事件：Claude Code 源码"误发"到 npm

一位 Anthropic 开发者把 debug 文件直接 push 到了**公开 npm 仓库**。
- **512,000+ 行** Claude Code 实际源码
- 内部 blog 草稿、**模型发布 checklist**、迁移文件
- 在"禁用版本字符串"列表里明晃晃写着：`Opus 4.7`、`Sonnet 4.8`
- 下一代代号被挖出：**Mythos**（据称比 Opus 更高一档）、**Capybara**、**Tengu**

> r/singularity 热评（206 赞）："Now we know why Opus 4.6 performed worse. So that the leap in quality of the next model would be more noticeable."
> —— 翻译成人话：4.6 最近的"67% 思维下降"不是 bug，是**Anthropic 自己在给 4.7 攒势能**。

### 🟨 Apr 4 · YouTube 首爆：BitBiasedAI 把 27K 人看懵

频道 **BitBiasedAI** 基于源码分析发布视频《Claude 4.7 Explained: 1M Context Window, 87% Benchmarks & AI Agents》，两天冲到 **27.8K 播放、683 赞**。核心爆点：
- **1M token 上下文窗口**
- **87%** 的新基准线成绩
- 上下文中**首次出现** `mythos` 模型代号

### 🟨 Apr 7 · Project Glasswing 启动

Anthropic 悄悄开放了 **Claude Mythos Preview**，只对安全研究合作方——**比 Opus 4.7 更高一档的模型已经在小范围测试**，这才是后面让 OpenAI 紧急发 GPT-5.4 Cyber 对线的真正原因。

### 🟦 Apr 13 · WorldofAI 把事情捅到主流圈

YouTube 频道 **WorldofAI**（96K 播放 · 2,041 赞 · 221 评论）确认了两件事：
- Claude Opus 4.7 **已出现在内部 API references**（这通常就是发布前夜的信号）
- Anthropic 一边准备发 4.7，一边悄悄给 4.6 做了"量化版"替换，用户感知到的"变笨"就是这么来的

### 🟥 Apr 14 晚 · The Information 独家：**本周发布**

《Exclusive: Anthropic Preps Opus 4.7 Model, AI Design Tool》——这是**信号等级从爆料跃升到"近官方"的那个瞬间**。The Information 是业内以"OpenAI/Google 内幕命中率极高"著称的付费刊物，措辞是非常克制的：

> "Anthropic is preparing its next flagship model, Claude Opus 4.7, along with a new AI-powered tool for designing websites and presentations, according to a person with knowledge of these products."

这里面藏了两颗炸弹：
1. **Opus 4.7 作为"flagship"** — Anthropic 仍不会把它定位为 Claude 5 那档的代际跃迁，但多步推理、长任务自治、多 agent 协同全部升级
2. **AI 设计工具** — 对齐 **Figma** 把生成代码转成可编辑设计稿 + 直接嵌入 **Word & PowerPoint**

---

## 2. 推特原文：Pankaj Kumar 一条推文吃掉 46 万浏览

The Information 在付费墙后，所以真正把消息"打"到开发者社群的是这条推文。

![X 原文截图 · @pankajkumar_dev · 2026-04-15](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/tweet-screenshot.png)

[原推地址 · x.com/pankajkumar_dev/status/2044281458999865495](https://x.com/pankajkumar_dev/status/2044281458999865495)

### 📎 他贴出来的图：Claude Opus 4.7 官方视觉（疑）

![Pankaj Kumar 推文附图 · Claude Opus 4.7 Banner](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/tweet-image-pankaj.jpg)

这张用的是 Anthropic 标志性的 **serif + 火花 logo + 米色纸张纹理**——和 Claude 官网 release post 一模一样的版式。社区普遍判断：**这就是官方发布海报提前泄漏**，或至少是基于 brand guideline 还原度极高的预热图。

### 🔑 推文原文翻译（结构化）

> **Claude Opus 4.7 据报本周就发，以下是要点：**
>
> - Anthropic 最快**本周**就会发布 Claude Opus 4.7，信息源来自 The Information 与内部代码库泄漏。
> - 与旗舰模型一起登场的是一款 **AI 驱动的设计工具**，输入自然语言就能生成整站、落地页和演示文稿。
> - 该工具**同时面向开发者和非技术用户**，直接威胁 Google Stitch 这类现存工具。
> - 内部 commit message 已经把下一代 Opus / Sonnet 的代号 **Capybara、Tengu** 都漏了出来。
> - 最近 Opus 4.6 那次"67% thinking drop"，现在看是**刻意为之**——为旗舰跃迁预留算力。
> - 竞争正在白热化，OpenAI 用 **GPT-5.4 Cyber**（防御性安全特化模型）回应，对标 Claude Mythos 高端线。

📊 **这条推的传播力**：
- 👁 **465,381** 次浏览
- ❤️ 3,351 赞 · 🔁 276 转推 · 💬 148 回复 · 🔖 839 收藏
- Pankaj 账号只有 8,007 粉丝——典型的"一条推干翻账号 Reach 天花板"

---

## 3. ⚡ "已经有人在系统里把 4.7 抓出来了" —— 3 条推特一手证据

公开 API 层面**还没看到 `claude-opus-4-7`**（OpenRouter / Vertex AI / platform.claude.com 均未上线），但**源码层面**已经有人把它拽出来了。下面三位是过去 5 天把这件事"坐实"的关键节点：

### ① @White1637402 · LLM Tester · 从 Claude Code 源码里挖到模型 ID

![@White1637402 的源码反编译截图 · capybara-v2-fast[1m]](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/internal-api-4.7-evidence.jpg)

这张是**整个爆料链路里最硬的一手证据**。@White1637402 的简介就是 "LLM Tester Hacker Builder"——他反编译 Claude Code 前端，在 `getMainLoopModel()` 的 switch 分支里直接看到：

```js
// ...and, for ants, model name '-fast' substring (ant-internal fast mode)
// like  capybara-v2-fast[1m]  encode speed in the model ID itself
```

几个关键信号**逐字拆给你**：
- **`capybara-v2-fast`** = 新一代模型的内部 ID 格式
- **`[1m]`** = 1M 上下文窗口
- **`USER_TYPE === 'ant'`** = Anthropic 内部账号才能触发这个模型（所以外部用户现在**调用会 403**）
- **`disableFastModeBreaker`** 这段熔断逻辑 = 模型**刚上线 staging**、内部还在做 A/B 的标志动作

> **翻译：4.7 不是 PPT 阶段，是已经在 `ant-internal` 账号跑对话的阶段。**

### ② @intheworldofai · 13K 粉丝科技号 · 把截图推向主流

![WorldofAI 引用 @White1637402 的推文](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/tweet-worldofai.png)

[原推地址 · x.com/intheworldofai/status/2043374449634726114](https://x.com/intheworldofai/status/2043374449634726114)

**133K 浏览 · 969 赞 · 64 转发**。WorldofAI 把 White 的反编译截图包装成"Opus 4.7 has been registered on internal API"，一夜之间成为主流开发者圈的共识梗——同时给出了**为什么 4.6 最近变笨**的解释：**资源已经在给 4.7 让路**。

### ③ @btibor91 · 38K 粉专业爆料人 · 4.7 进入"近官方"时刻

![Tibor Blaho 引用 The Information 确认](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/tweet-tibor.png)

[原推地址 · x.com/btibor91/status/2044112780874121672](https://x.com/btibor91/status/2044112780874121672)

Tibor Blaho 是**业内公认最准的 Anthropic/OpenAI 爆料人**——当年 Opus 4、Opus 4.5 发布前他全部提前命中。**134.8K 浏览 · 921 赞**。他出手引用 The Information 这一刻，等同于"非官方但近官方"确认。

### ④ @bridgemindai · 27K 粉 · 用数学证明"就在这周"

![BridgeMind 用节拍推算发布窗口](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/20260416/tweet-bridgemind.png)

[原推地址 · x.com/bridgemindai/status/2044007189107331487](https://x.com/bridgemindai/status/2044007189107331487)

**160.7K 浏览 · 1,113 赞**。一条冷静的节拍推算：
- Opus 4.5：**2025-11-24** 发布
- Opus 4.6：73 天后，**2026-02-05** 发布
- 今天：距 4.6 第 **68** 天
- **→ 发布窗口 = 本周或下周**
- + 同样的"降智 → 替换"模式已经在 4.5 → 4.6 上演过一次

---

> **重要校准**：@White1637402 发现的 `capybara-v2` 这个 token，严格讲是 **Mythos / Capybara tier**（比 Opus 高一档）的代号，WorldofAI 把它解读为 "Opus 4.7" 是快思考下的简化。**但无论如何，这个证据链里有两件事已经板上钉钉**：
> 1. Anthropic 的下一代多个模型（4.7 / 4.8 / Mythos）**已经在内部 API 跑真实对话**
> 2. 外部用户还拿不到 4.7 本身，**能直接"测到"的是 Claude Code 里的源码证据**

---

## 4. 一张图能看到几个关键信号

| 信号 | 证据 | 含义 |
| --- | --- | --- |
| 4.6 的"降智"是策略性的 | r/singularity 206 赞热评 + WorldofAI 视频 | 资源提前让位给 4.7 训推 |
| Opus 4.7 **不是**最强的那个 | Project Glasswing 已在跑 **Mythos** | 4.7 只是"旗舰递进"，真正的"代际跃迁"可能藏在 Claude 5 |
| 设计工具会直接卷一级股 | FIG / WIX / ADBE / GDDY 当日均跌 2.7–6% | 市场对"Anthropic 做 Stitch + Figma 对标"这件事**高度相信真实性** |
| 发布窗口在眼前 | 今天是"this week"第 3 天，剩 Thu/Fri | Polymarket 在 **99%** 概率上 stick 不动 |

---

## 5. 社区在担心的 3 件事

1. **额度一次就挂**
   > r/singularity 71 赞评论："Can't wait to hit my limits after 1 prompt."
   Opus 系列 Pro 用户的 quota 焦虑已经是常态，4.7 更强也意味着单次调用更贵，发布当日大概率会重演"午夜打限流"。

2. **4.6 是不是"悄悄被换成量化版"**
   WorldofAI 的视频里给出了最直接的怀疑链路：当前在线的 Opus 4.6 在某些 benchmark 上相对发布时表现下滑；4.7 一旦发布，官方可能会把它重新标回"unquantized"或干脆下线。

3. **"设计工具"到底是 Claude 的新产品，还是 Claude Code 的插件**
   截至写稿，两种说法都有——The Information 用的是 "new AI-powered tool"，暗示**独立产品线**；但 Claude Code 近期又静悄悄上了 `/ultraplan`、`/powerup`、`/insights` 三条命令，走的是插件路子。**本周发布时会揭晓。**

---

## 6. 接下来 72 小时，盯这三件事

- 🚨 **本周四/周五的 `anthropic.com/news`**——"this week" 只剩两个工作日
- 📊 **Polymarket 上 "Claude 4.7 released by May 31?" 市场**的跳点
- 🎛 **API 列表里是否出现 `claude-opus-4-7-20260416`**（YYYY-MM-DD 后缀是 Anthropic 固定发布格式）

> 一句话判断：**这次不是"会不会发"，而是"周几发"**。

---

## 📚 原始资料（可追溯）

- **@White1637402 源码反编译**（引发一切的原点）：从 Claude Code 前端里挖到 `capybara-v2-fast[1m]`
- **WorldofAI 主流放大**：[x.com/intheworldofai/status/2043374449634726114](https://x.com/intheworldofai/status/2043374449634726114) · 133K 浏览
- **Tibor Blaho 近官方确认**：[x.com/btibor91/status/2044112780874121672](https://x.com/btibor91/status/2044112780874121672) · 134K 浏览
- **BridgeMind 节拍推算**：[x.com/bridgemindai/status/2044007189107331487](https://x.com/bridgemindai/status/2044007189107331487) · 160K 浏览
- **Pankaj Kumar 要点聚合**：[x.com/pankajkumar_dev/status/2044281458999865495](https://x.com/pankajkumar_dev/status/2044281458999865495) · 465K 浏览
- **The Information 独家（付费墙）**：[theinformation.com — Exclusive: Anthropic Preps Opus 4.7 Model, AI Design Tool](https://www.theinformation.com/briefings/exclusive-anthropic-preps-opus-4-7-model-ai-design-tool)
- **Reddit r/singularity 原帖（882 赞）**：[Anthropic is set to release Claude Opus 4.7 and a new AI design tool as early as this week](https://www.reddit.com/r/singularity/comments/1slh72j/anthropic_is_set_to_release_claude_opus_47_and_a/)
- **Reddit r/ArtificialInteligence**：[Anthropic May Be About to Launch Claude Opus 4.7](https://www.reddit.com/r/ArtificialInteligence/comments/1sm48m9/anthropic_may_be_about_to_launch_claude_opus_47/)
- **YouTube BitBiasedAI**：[Claude 4.7 Explained: 1M Context, 87% Benchmarks](https://www.youtube.com/watch?v=js-rzAvoOwI) · 27.8K views
- **YouTube WorldofAI**：[Claude Opus 4.7 Leaked + Anthropic Full Stack App + ...](https://www.youtube.com/watch?v=MXZ9cfkYuRI) · 96.5K views
- **Medium · The Claude Code Leak**：[Opus 4.7, Sonnet 4.8, and Mythos — A Rare, Unfiltered Look Inside Anthropic](https://medium.com/@mfierce0/the-claude-code-leak-opus-4-7-sonnet-4-8-and-mythos-a-rare-unfiltered-look-inside-anthropic-70c6f735810a)
- **Polymarket**：[Claude 4.7 released by ...](https://polymarket.com/event/claude-4pt7-released-by)
- **Geeky Gadgets · 深度解读**：[Claude Opus 4.7 Leaks & Anthropic's Full-Stack AI Studio](https://www.geeky-gadgets.com/claude-opus-4-7-leak-anthropic-updates/)
- **Dataconomy · 发布预期**：[Anthropic To Launch Claude Opus 4.7 This Week](https://dataconomy.com/2026/04/15/anthropic-to-launch-claude-opus-4-7-this-week/)
- **The Tech Portal · 报道**：[Anthropic could soon release Opus 4.7 model and AI design tool](https://thetechportal.com/2026/04/15/anthropic-could-soon-release-opus-4-7-model-and-ai-design-tool/)

---

*数据日期：2026-04-16 傍晚更新 · 跨源取证：X/Twitter（fxtwitter API 直取结构化数据）+ Reddit / YouTube / TikTok / Instagram / Polymarket / Web（Brave、Serper）· 60+ 条证据、5 大数据源聚合 · 新增 4 条 Twitter 一手截图。*
