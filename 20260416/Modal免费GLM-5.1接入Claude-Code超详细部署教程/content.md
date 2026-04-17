# Modal 免费 GLM-5.1 接入 Claude Code 超详细部署教程（2026-04 限时白嫖版）

![Modal 免费白嫖 GLM-5.1 封面](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-glm5-cover.png)

> 发布日期：2026-04-16  
> 标签：#ClaudeCode #GLM-5.1 #Modal #LiteLLM #AI编程  
> 截止日期：**2026-04-30**（Modal 免费窗口关闭）  
> 适用人群：被 Claude Code 账单割肉的开发者、想体验对标 Opus 4.5 的开源模型的人

---

## 0. 你为什么需要这一篇

2026 年 2 月，Anthropic 在 ToS 里加了一条**毫无回旋余地的话**：

> "The use of OAuth tokens obtained via Claude Free, Pro, or Max accounts in any other product, tool, or service is not permitted."

翻译：**Claude Free / Pro / Max 订阅拿到的 OAuth Token，不允许用在 Claude Code 之外的任何工具上**。OpenClaw / OpenCode / Cline 借用 Max 订阅认证一夜之间 401，过去半年最热的"省钱姿势"全部失效。

要么真金白银按 token 付钱，要么——**找一条合法的免费替代路**。这篇就是其中一条：**Z.ai 最新开源的 GLM-5.1（对标 Opus 4.5）+ Modal 官方免费托管**。

---

## 1. 这次到底白嫖了什么

![GLM-5 Pricing Free until April 30th](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-pricing-free.png)

Modal Research 团队和 Z.ai 合作，把 GLM-5.1 部署到 Modal 自家基础设施上，**给所有人开了一个免费公共 endpoint**：

| 项目 | 数值 |
|---|---|
| **模型** | `zai-org/GLM-5-FP8`（GLM-5.1，FP8 量化） |
| **大小** | 700GB（FP8）/ 1.65TB（原版） |
| **架构** | MoE 稀疏 + DeepSeek Sparse Attention |
| **上下文窗口** | 192,000 tokens |
| **单次最大输出** | 8,192 tokens |
| **吞吐** | 30–75 tok/s |
| **限制** | 单并发请求（一个 client 一次只能跑一个请求） |
| **Token 限制** | 无（按请求次数计） |
| **许可证** | MIT 开源 |
| **官方对标** | Anthropic Opus 4.5 |
| **费用** | **2026-04-30 之前 $0** |
| **入口** | <https://modal.com/glm-5-endpoint> |

**性能基准**（来自 Modal 官方博客）：

![GLM-5 Benchmark](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/glm5-benchmark.png)

可以看到 GLM-5.1 在编程、推理、数学等多项基准上接近甚至超越 Opus 4.5。

---

## 2. 接入原理（不要跳过这段）

Claude Code **只吃三种 API 格式**（[官方文档明确写](https://code.claude.com/docs/en/llm-gateway)）：
1. Anthropic Messages（`/v1/messages`）
2. Bedrock InvokeModel
3. Vertex rawPredict

**Modal 的 GLM-5.1 endpoint 是 OpenAI 兼容**（`/v1/chat/completions`），**直接连不通**。所以中间必须有一层翻译。

最优雅的方案是 Modal 官方 [modal-projects/modal-jazz](https://github.com/modal-projects/modal-jazz/tree/main/frontends/claude) 仓库给的：**把 LiteLLM 容器化部署到 Modal serverless 上**——本地零 Python 依赖、官方 Docker 镜像（避开 LiteLLM PyPI 1.82.7/1.82.8 的供应链投毒事件）、永久在线、3 条命令搞定。

链路就是：

```
你的电脑 (Claude Code)
    ↓ Anthropic /v1/messages
Modal Serverless (LiteLLM Container) ← 这层做协议翻译
    ↓ OpenAI /chat/completions
Modal GLM-5.1 Endpoint (zai-org/GLM-5-FP8)
```

---

## 3. 完整部署步骤（每一步都有验证）

### Step 1 — 注册 Modal 账号 + 装 CLI

#### 1.1 打开 Modal GLM-5 endpoint 页面

浏览器访问 <https://modal.com/glm-5-endpoint>，第一次访问会要求登录：

![Modal sign in](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-step1-signin.png)

点 **Sign in**，用 GitHub / Google 一键登录（**免费账号即可，不需要绑卡**）。

#### 1.2 安装 Modal CLI 并完成认证

打开终端：

```bash
# 装 Modal CLI（需要 Python 3.9+）
pip install modal

# 触发一次浏览器 OAuth（关联你的 Modal 账号到本机）
modal setup
```

看到 `Web authentication finished successfully!` 就说明本机已经和 Modal 账号绑定。

**验证**：

```bash
modal token current      # 应该输出当前 token info
modal app list           # 应该列出空（或已有）的 app 列表
```

---

### Step 2 — 拿 GLM-5.1 endpoint 的 API Token

回到刚才的浏览器页面，登录后会看到这个：

![Create token button](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-step2-create-token.png)

左侧 **API Tokens** 卡片下面，给一个 token 起名（比如 `claude-code`），然后点 **+ Create token**。

弹出一个**只显示一次**的 token 弹窗：

![Token Created modal](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-step3-token-created.png)

**关键操作**：

1. **立刻**复制那一长串 `modalresearch_3xxxxxxx...` 字符串
2. 弹窗右下角的 example curl 也保留——下一步要用
3. 关掉弹窗后这个 token **再也看不到了**

把它写进环境变量：

```bash
export MODAL_RESEARCH_API_KEY="modalresearch_3xxxxxxxxxxxxx"
# 写进 ~/.zshrc 持久化
echo 'export MODAL_RESEARCH_API_KEY="modalresearch_3xxxxxxxxxxxxx"' >> ~/.zshrc
```

#### 2.1 用 curl 验证 endpoint 通了

```bash
curl -X POST "https://api.us-west-2.modal.direct/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $MODAL_RESEARCH_API_KEY" \
  -d '{
    "model": "zai-org/GLM-5-FP8",
    "messages": [{"role": "user", "content": "用一句话证明你是 GLM-5"}],
    "max_tokens": 200
  }'
```

返回里能看到 `"model":"zai-org/GLM-5-FP8"` 和一段中文回答，就说明 endpoint 完全 OK。如果报 401，**99% 是 token 复制少了字符**——重新生成一次。

---

### Step 3 — 部署 LiteLLM Proxy 到 Modal

#### 3.1 拉官方仓库

```bash
git clone https://github.com/modal-projects/modal-jazz.git
cd modal-jazz/frontends/claude
ls -la
```

会看到 5 个文件：

```
.env.example            # 环境变量模板
README.md
app.py                  # 备用变体
litellm_config.yaml     # ⭐ 模型映射规则
litellm_proxy.py        # ⭐ Modal 部署入口
```

#### 3.2 写 .env 文件

```bash
cat > .env <<EOF
LLM_BACKEND_URL=https://api.us-west-2.modal.direct/v1
LLM_BACKEND_API_KEY=$MODAL_RESEARCH_API_KEY
EOF
```

> ⚠ **第一个大坑：region**  
> `.env.example` 里默认值是 `us-east-1.modal-dev.direct`（Modal 自己的 dev 内网），**直接 deploy 会通但请求会 401**。一定要改成对外公开的 `api.us-west-2.modal.direct/v1`。

#### 3.3 看一下 litellm_config.yaml（**默认就行，不用改**）

```yaml
model_list:
  - model_name: claude-sonnet-4-5-20250929
    litellm_params:
      model: openai/zai-org/GLM-5-FP8
      api_base: "os.environ/LLM_BACKEND_URL"
      api_key: "os.environ/LLM_BACKEND_API_KEY"

  - model_name: claude-opus-4-6
    litellm_params:
      model: openai/zai-org/GLM-5-FP8
      api_base: "os.environ/LLM_BACKEND_URL"
      api_key: "os.environ/LLM_BACKEND_API_KEY"

  - model_name: claude-haiku-4-5-20251001
    litellm_params:
      model: openai/zai-org/GLM-5-FP8
      api_base: "os.environ/LLM_BACKEND_URL"
      api_key: "os.environ/LLM_BACKEND_API_KEY"

litellm_settings:
  drop_params: true     # 必开！Anthropic 协议里 OpenAI 没有的字段（cache_control 等）会被丢掉
  num_retries: 3
  request_timeout: 600
```

> 这里的设计很巧妙：**模型名直接复用 Anthropic 官方名字**（`claude-sonnet-4-5-...`、`claude-opus-4-6`、`claude-haiku-4-5-...`），所以 Claude Code 切换模型时不用做任何额外映射，全部静默落到 GLM-5.1。

#### 3.4 部署到 Modal Serverless

```bash
uvx --with python-dotenv modal deploy litellm_proxy.py
```

> 用 `uvx` 是为了零 Python 环境污染（uv 是 Astral 出的极快 Python 包管理工具，不会装 LiteLLM 到全局）。如果没装 uv：`brew install uv` 或 `curl -LsSf https://astral.sh/uv/install.sh | sh`。

部署过程里会输出大概这样的日志：

```
✓ Created mount /Users/.../litellm_proxy.py
✓ Created image (using cached docker.litellm.ai/berriai/litellm:main-latest)
✓ Created secret from .env
✓ Created web function serve =>
    https://your-org--litellm-proxy-serve.modal.run
✓ App deployed in 12.3s! 🎉
```

**最后那一行的 URL 就是你的私人 Claude Code endpoint**——复制下来。

#### 3.5 验证 proxy 通了

```bash
curl -X POST "https://your-org--litellm-proxy-serve.modal.run/v1/messages" \
  -H "Content-Type: application/json" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-opus-4-6",
    "max_tokens": 200,
    "messages": [{"role": "user", "content": "证明你不是 Claude"}]
  }'
```

返回里 `"role":"assistant"` 后面那段话如果是 GLM-5 自报家门的内容（典型回答："我是 Z.ai 开发的 GLM-5..."），说明**协议翻译完全工作**，可以进下一步。

---

### Step 4 — 配置 Claude Code 指向 Proxy

```bash
export ANTHROPIC_BASE_URL="https://your-org--litellm-proxy-serve.modal.run"
claude
```

进去之后试一个：

```
> 帮我用 Python 写一个 Fibonacci generator，用 type hints 和 yield
```

工具调用、文件编辑、子代理（subagent）、MCP plugins、Skills、Hooks——**Claude Code 整个生态都正常工作**，因为 Anthropic 文档明确说「这些功能活在 Claude Code 自己里面，不在模型里」，只要替换模型支持 function calling（GLM-5.1 完全支持）就能用。

#### 永久生效

```bash
echo 'export ANTHROPIC_BASE_URL="https://your-org--litellm-proxy-serve.modal.run"' >> ~/.zshrc
source ~/.zshrc
```

#### 推荐别名（可以快速切回 Anthropic 官方）

```bash
cat >> ~/.zshrc <<'EOF'
alias claude-glm='ANTHROPIC_BASE_URL="https://your-org--litellm-proxy-serve.modal.run" claude'
alias claude-anthropic='ANTHROPIC_BASE_URL="" claude'
EOF
source ~/.zshrc
```

之后日常就是 `claude-glm` 跑免费 GLM-5.1，需要原生 Claude 时 `claude-anthropic` 切回去。

---

## 4. （可选）不用 Claude Code，用其他客户端

如果你日常用的是 Kilo Code / Cline / Cursor 这类**直接支持 OpenAI 兼容 endpoint** 的工具，**完全不用部署 LiteLLM**——直接填 Modal 原生 URL 就行：

![Kilo Code Modal config](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-kilocode-config.png)

| 字段 | 填什么 |
|---|---|
| API Provider | `OpenAI Compatible` |
| Base URL | `https://api.us-west-2.modal.direct/v1` |
| API Key | 你的 `MODAL_RESEARCH_API_KEY` |
| Model | `zai-org/GLM-5-FP8` |

实际效果：

![Kilo Code running GLM-5](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-glm5-running.png)

192K 上下文 + Plan/Code/Architect mode 全部支持。

---

## 5. 真实坑位排雷（每一条都是血泪）

### ❶ region 默认值的坑（已强调）

`.env.example` 默认 `us-east-1.modal-dev.direct` 是 Modal 内网，必须改 `us-west-2.modal.direct`。

### ❷ LiteLLM 的 PyPI 投毒事件

**不要**在本地 `pip install litellm` 装 1.82.7 或 1.82.8 版本——这两个版本被注入了凭据窃取代码（[BerriAI/litellm#24518](https://github.com/BerriAI/litellm/issues/24518)，[Anthropic 官方文档警告](https://code.claude.com/docs/en/llm-gateway)）。Modal 方案用的是官方 Docker 镜像 `docker.litellm.ai/berriai/litellm:main-latest`，避开了这个雷。

### ❸ `drop_params: true` 必开

Anthropic 协议里有 `cache_control`、`tool_use_id` 这些字段，OpenAI 协议没有。不开这个开关 LiteLLM 会直接报 422。官方 yaml 里默认开了，**别去改**。

### ❹ 单并发的真实体感

一个对话窗口同时只能发一个请求。**两个 Claude Code 窗口同时让 GLM-5.1 跑长任务，第二个会报 429 或被阻塞**。如果你需要并行，要么排队、要么用方案 B（NVIDIA NIM 见姊妹篇）。

### ❺ 部署的 URL 默认公网可访问

`*.modal.run` URL **任何人拿到都能用**——会消耗你的 Modal credit。**强烈推荐加鉴权**，在 `litellm_config.yaml` 里加：

```yaml
general_settings:
  master_key: sk-我自己设的强密码-至少32位
```

然后 Claude Code 端额外设：

```bash
export ANTHROPIC_AUTH_TOKEN="sk-我自己设的强密码-至少32位"
```

### ❻ 4-30 后的兜底

- **Modal 不续期**：迁到 NVIDIA NIM Minimax M2.7（[姊妹篇](#)，永久免费 1000 credits）
- **想继续用 GLM-5.1**：去 z.ai 订 GLM Coding Plan（最低 $3/月起，是 Claude Pro 的 1/7）
- **彻底独立**：用 Unsloth 把 GLM-5.1 量化到 220GB，自己 4×A100 跑

### ❼ Modal 免费额度怎么算

Modal Starter 套餐每月送 $30 信用额度。**这个 LiteLLM proxy 部署消耗几乎可以忽略**（按 web request 计费，每月几分钱），实际成本约等于零。真正的 GLM-5.1 推理成本是 Modal Research 团队替你出的。

---

## 6. 编码体感（HN + Reddit 综合）

- **优点**：
  - 长程任务（>100 轮工具调用）的连贯性确实在线，HN 上有人说「比 Sonnet 4.5 在 refactor 大型代码库时更稳」
  - 中文表现意外地好（Z.ai 是国内团队）
  - MIT 协议商用零顾虑
- **缺点**：
  - 30-75 tok/s 比 Anthropic 自营快，但和 Groq 这种推理极速派比慢
  - **不支持 vision**（纯文本编程）
  - 单并发 → 不能并行 batch agent
- **盲区**：当前 endpoint 应该没开 prefix caching，长上下文重复使用时不像 Anthropic 那么省

---

## 7. 配套资源（全部一手源）

- **Modal 官方 Claude Code 模板** ⭐：<https://github.com/modal-projects/modal-jazz/tree/main/frontends/claude>
- **Modal GLM-5.1 endpoint**：<https://modal.com/glm-5-endpoint>
- **Modal 官方介绍博客**：<https://modal.com/blog/try-glm-5>
- **GLM-5.1 GitHub**：<https://github.com/zai-org/GLM-5>
- **Hugging Face 权重**：<https://huggingface.co/zai-org/GLM-5.1>
- **Claude Code Gateway 官方文档**：<https://code.claude.com/docs/en/llm-gateway>
- **LiteLLM 投毒事件追踪**：<https://github.com/BerriAI/litellm/issues/24518>
- **Modal 推特官宣**：[@modal](https://x.com/modal/status/2041820290716729379)

> 📌 想看 NVIDIA NIM × MiniMax M2.7（**永久免费 1000 credits、40 RPM**）的姊妹方案？请看 → `../NVIDIA-NIM免费MiniMax-M2.7接入Claude-Code超详细部署教程/content.md`  
> 强烈建议两个都部署，在 LiteLLM 里做 fallback：M2.7 跑日常、GLM-5.1 跑长程任务。

---

## 8. 写在最后

这波操作的本质：

- **智谱**用 MIT 开源 GLM-5.1 + 对标 Opus 4.5 的性能，撕开海外开发者的入口
- **Modal** 用免费托管 + 一键部署模板，把 serverless 平台的认知度打透
- **Anthropic** 在 OAuth 一刀切之后短期不会理，但**当 GLM-5.1 + Claude Code 这条链路被 Modal 官方亲自维护**，"Claude Code 必须配 Anthropic 模型"这个心智壁垒就开始裂了

> **抓手在这里**：今晚 5 分钟跑通官方模板，明天用一周对比 Anthropic 账单。数据出来你自己就知道值不值得长期投入。最坏情况——4 月 30 日窗口关闭，你也已经摸清「开源模型 + LiteLLM + 自部署」整条链路，下次任何免费窗口出现（一定会有），5 分钟切过去。

---

*— AI-Tracker 2026-04-16 · Modal × GLM-5.1 详细部署篇*
