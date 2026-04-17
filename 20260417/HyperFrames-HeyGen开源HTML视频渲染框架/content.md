# HyperFrames: HeyGen 开源的 AI-First 视频渲染框架 -- 用 HTML 写视频

![HyperFrames Cover](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/hyperframes-cover.png)

> Write HTML. Render Video. Built for Agents.

![HyperFrames Demo](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/hyperframes-demo.gif)

## 什么是 HyperFrames?

[HyperFrames](https://github.com/heygen-com/hyperframes) 是 HeyGen 刚刚开源的视频渲染框架，核心理念极其简洁：**用 HTML 写视频，让 AI Agent 来做导演**。

不需要 React，不需要专有 DSL，不需要 After Effects。你只需要写 HTML + CSS + JavaScript，HyperFrames 就能把它渲染成 MP4、MOV 或 WebM 视频。

最新版本 **v0.4.2** 于 2026 年 4 月 16 日发布。项目创建仅 5 周，已收获 **777 Stars**、**69 Forks**、**327 Commits**，正处于快速迭代期。

## 为什么值得关注?

### 1. AI Agent 原生设计

这不是一个"顺便支持 AI"的工具。HyperFrames 从第一天就为 AI Agent 设计：

- CLI 默认非交互模式，天然适配自动化流水线
- 内置 Skills 系统，支持 Claude Code、Cursor、Gemini CLI、Codex
- Agent 描述需求 → 写 HTML → 预览 → 渲染 → MP4，全程零人工干预

```bash
# 一条命令教会你的 AI Agent 做视频
npx skills add heygen-com/hyperframes
```

### 2. HTML 即视频

视频的每一帧都是一个 HTML 页面。这意味着：

- 前端开发者零学习成本
- CSS 动画、GSAP、Three.js、Lottie 全部可用
- 确定性渲染：同样的输入 = 完全一致的输出

```html
<div id="stage" data-composition-id="my-video"
     data-start="0" data-width="1920" data-height="1080">
  <video id="clip-1" data-start="0" data-duration="5"
         data-track-index="0" src="intro.mp4" muted playsinline></video>
  <img id="overlay" class="clip" data-start="2" data-duration="3"
       data-track-index="1" src="logo.png"/>
  <audio id="bg-music" data-start="0" data-duration="9"
         data-track-index="2" data-volume="0.5" src="music.wav"></audio>
</div>
```

### 3. 完整的工具链

HyperFrames 不是一个单文件脚本，而是一套精心设计的 monorepo：

![HyperFrames Architecture](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/hyperframes-arch.png)

| 包名 | 功能 |
|------|------|
| `hyperframes` (CLI) | 创建、预览、lint、渲染 |
| `@hyperframes/core` | 类型、解析器、linter、运行时、Frame Adapters |
| `@hyperframes/engine` | Puppeteer + FFmpeg 逐帧捕获引擎 |
| `@hyperframes/producer` | 完整渲染管线：捕获 + 编码 + 音频混合 |
| `@hyperframes/studio` | 浏览器端编辑器 |
| `@hyperframes/player` | 可嵌入的 `<hyperframes-player>` Web Component |
| `@hyperframes/shader-transitions` | WebGL 着色器转场效果 |

### 4. 50+ 现成组件

内置 Catalog 提供社交媒体叠层、着色器转场、数据可视化、电影级特效等组件：

```bash
npx hyperframes add flash-through-white   # 闪白转场
npx hyperframes add instagram-follow      # Instagram 关注叠层
npx hyperframes add data-chart            # 动画图表
```

## 社区反响

X (Twitter) 上的开发者博主 [Rohan Paul](https://x.com/rohanpaul_ai/status/2044851401118138572) 这样评价：

> "HeyGen just open-sourced HyperFrames, it lets AI agents turn HTML, CSS, and JavaScript into MP4, MOV, or WebM video from the terminal. An AI-agent-first renderer for video."

项目当前有 3 位核心贡献者（jrusso1020: 171 commits, miguel-heygen: 100, vanceingalls: 51），开发节奏非常活跃。最新的 roadmap 显示 HDR 多层合成、composition authoring pipeline 等重磅功能正在推进中。

## 快速上手

```bash
# 方式一：让 AI Agent 帮你做（推荐）
npx skills add heygen-com/hyperframes
# 然后告诉你的 Agent："用 /hyperframes 做一个 10 秒产品介绍视频"

# 方式二：手动创建
npx hyperframes init my-video
cd my-video
npx hyperframes preview      # 浏览器预览（热重载）
npx hyperframes render       # 渲染为 MP4
```

**环境要求**：Node.js >= 22, FFmpeg

## 核心技术亮点

- **Frame Adapter 模式**：可插拔的动画运行时（GSAP、Lottie、CSS、Three.js）
- **确定性渲染**：Puppeteer 逐帧截图 + FFmpeg 编码，相同输入永远输出相同视频
- **CRF/Bitrate 控制**：v0.4.0 新增，可精确控制输出质量
- **Website Capture Pipeline**：可以直接把网站录制成视频
- **TTS + 字幕同步**：内置转写和语音合成能力

## 适用场景

- **内容创作者**：用自然语言描述想要的视频，AI Agent 自动生成
- **开发者**：用熟悉的 HTML/CSS/JS 技术栈做视频
- **自动化管线**：CI/CD 中自动生成产品演示、changelog 视频
- **数据可视化**：CSV → 动画图表竞赛视频
- **社交媒体**：批量生成 TikTok/Reels 格式的短视频

## 一句话总结

HyperFrames 把视频制作从"专业工具 + 手动操作"变成了"写 HTML + AI 自动化"。如果你是前端开发者，或者正在构建 AI Agent 工作流，这个项目值得立刻 star。

---

- GitHub: https://github.com/heygen-com/hyperframes
- 文档: https://hyperframes.heygen.com/introduction
- 许可证: Apache 2.0
