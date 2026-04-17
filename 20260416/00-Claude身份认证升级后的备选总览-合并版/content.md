# Claude 身份认证升级后，我们还有哪些白嫖备选？——Modal GLM-5.1 与 NVIDIA Minimax M2.7 双线接入 Claude Code 全教程

> 发布日期：2026-04-16  
> 标签：#ClaudeCode #GLM-5.1 #MinimaxM2.7 #Modal #NVIDIA-NIM #LiteLLM #AI编程  
> 关键时间窗：Modal GLM-5.1 免费截止 **2026-04-30**；NVIDIA NIM Minimax M2.7 免费 1000 credits（可申请到 5000）

---

## 起因：Anthropic 在 2026 年 2 月把后门焊死了

先把背景对齐——这事儿不是单纯的"省钱"教程，而是被官方政策推着走的。

2026 年 2 月，Anthropic 更新 ToS，加了一句**毫无回旋余地的话**：

> "The use of OAuth tokens obtained via Claude Free, Pro, or Max accounts in any other product, tool, or service is not permitted."

翻译过来就是：**Claude Free / Pro / Max 订阅拿到的 OAuth Token，不允许用在 Claude Code 之外的任何工具上**。

这一刀直接砍掉了过去半年里最热的两个"省钱姿势"：

- **OpenClaw / OpenCode / Cline 等借用 Max 订阅认证** —— 一夜之间 401
- **claude-code-router 转发 Max Token 给其他客户端** —— 同样被堵
- **Sonnet 4.6 发布后**，违规账号开始批量收警告甚至封禁

也就是说，过去那种"一份 $20 的 Max 订阅养活全家"的玩法**已经走不通了**。要么真金白银按 token 付钱给 Anthropic，要么——**找一条合法的免费替代路**。

这篇就是给你两条。

---

## 一句话总结两个方案

| | 方案 A · Modal GLM-5.1 | 方案 B · NVIDIA Minimax M2.7 |
|---|---|---|
| **模型** | Z.ai GLM-5.1（700GB FP8） | MiniMax M2.7（230B MoE） |
| **对标** | Opus 4.5 | Sonnet 4.5 / 编程+Office |
| **上下文** | 192K | 204K |
| **限制** | 单并发请求 | 40 RPM（请求/分钟） |
| **免费窗口** | 截止 2026-04-30 | 1000 credits / 永久（用完才付费） |
| **接入** | Modal 官方 LiteLLM 模板 | LiteLLM 直连 NVIDIA endpoint |
| **协议** | OpenAI 兼容（需翻译） | OpenAI 兼容（需翻译） |
| **适合** | 长程 Agent、超大重构 | 中等并发、日常编程、Office 类 |

![Claude 认证升级后的两个白嫖备选](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-vs-nvidia-compare.png)

---

## 方案 A · Modal × GLM-5.1（官方 modal-jazz 模板）

### 背景

Z.ai 4 月发布 GLM-5.1，定位**长程 Agent 与系统工程**：

- **192K 上下文 / 8K 输出 / 700GB FP8**
- MoE + DeepSeek Sparse Attention
- **MIT 开源**
- 官方对标 Anthropic Opus 4.5

Modal Research 拿这模型做了个**免费公开 endpoint** 给所有人试用：

- 即日起到 **2026-04-30** 全免费
- **单并发**（一个 client 一次只能跑一个请求）
- 没有 token 数量限制
- 30–75 tok/s

API 格式是 **OpenAI 兼容**（`/v1/chat/completions`），Claude Code 默认讲 Anthropic Messages API，所以中间需要翻译。

### 接入架构

![Modal × GLM-5.1 × Claude Code 接入架构](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-glm5-architecture-v2.png)

### 5 分钟接入流程（用 Modal 官方 modal-jazz 仓库）

> **底层逻辑**：把 LiteLLM 容器化部署到 Modal serverless 上，本地零 Python 依赖。这是 Modal 官方在 [modal-projects/modal-jazz](https://github.com/modal-projects/modal-jazz/tree/main/frontends/claude) 仓库里给的标准方案。

![5 分钟接入流程 · Modal 官方方案](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/modal-glm5-steps-v2.png)

#### Step A1 — 取 Modal Key + 装 Modal CLI

```bash
# 1. 在浏览器打开 https://modal.com/glm-5-endpoint
#    用 GitHub/Google 登录，点 "Generate API Key"，复制 ak-xxxxxx

# 2. 装 Modal CLI 并认证
pip install modal
modal setup       # 浏览器自动 OAuth
```

#### Step A2 — 拉官方仓库

```bash
git clone https://github.com/modal-projects/modal-jazz.git
cd modal-jazz/frontends/claude
cat > .env <<EOF
LLM_BACKEND_URL=https://api.us-west-2.modal.direct/v1
LLM_BACKEND_API_KEY=ak-你刚才复制的key
EOF
```

> ⚠ **region 坑位**：`.env.example` 里默认是 `us-east-1.modal-dev.direct`（Modal 内网），公开 endpoint 是 `api.us-west-2.modal.direct/v1`，**一定要改**。

`litellm_config.yaml` 不用改，官方默认把 sonnet/opus/haiku 全部映射到 `zai-org/GLM-5-FP8`。

#### Step A3 — 部署到 Modal Serverless

```bash
uvx --with python-dotenv modal deploy litellm_proxy.py
```

部署完会输出一个 URL：`https://your-org--litellm-proxy-serve.modal.run`

#### Step A4 — 起 Claude Code

```bash
export ANTHROPIC_BASE_URL="https://your-org--litellm-proxy-serve.modal.run"
claude
```

工具调用、Skills、MCP、子代理全部正常工作（因为 Anthropic 文档明确说「这些功能活在 Claude Code 自己里面，不在模型里」，只要替换模型支持 function calling 就能用，GLM-5.1 完全支持）。

---

## 方案 B · NVIDIA NIM × MiniMax M2.7（永久免费 1000 credits）

### 背景

老黄（NVIDIA）也在搞**免费 AI 推理云**了——`build.nvidia.com` 平台，注册即送 **1000 推理 credits**，可申请到 5000，**40 RPM** rate limit。M2.7 是 2026-04-11 上线的最新模型。

**MiniMax M2.7 关键参数**：

- **230B 参数 MoE**（256 个 local experts，每 token 激活 8 个）
- **204K 上下文**（比 GLM-5.1 的 192K 还大一点）
- **官方定位**：复杂软件工程 + agentic 工具调用 + 办公生产力
- **Apache 2.0 开源**（[GitHub](https://github.com/MiniMax-AI/MiniMax-M2.7) / [HF 权重](https://huggingface.co/MiniMaxAI/MiniMax-M2.7)）
- **API 完全 OpenAI 兼容**

### 接入流程

#### Step B1 — 拿 NVIDIA NIM API Key

1. 访问 <https://build.nvidia.com/minimaxai/minimax-m2.7>
2. 用 GitHub / Google / 企业邮箱登录
3. 右上角点 "Get API Key"，复制 `nvapi-xxxxxx`
4. 验证一下：

```bash
curl https://integrate.api.nvidia.com/v1/chat/completions \
  -H "Authorization: Bearer $NVIDIA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "minimaxai/minimax-m2.7",
    "messages": [{"role": "user", "content": "Hello, who are you?"}],
    "max_tokens": 200
  }'
```

返回里能看到模型名就说明通了。

#### Step B2 — 复用 modal-jazz 模板，加一个 NVIDIA provider

如果你已经按方案 A 部署过 LiteLLM proxy，**只需要改 `litellm_config.yaml`，加一段 NVIDIA 的映射**。无需重起、无需新 endpoint。

```yaml
model_list:
  # === 方案 A：GLM-5.1（保留） ===
  - model_name: claude-opus-4-6
    litellm_params:
      model: openai/zai-org/GLM-5-FP8
      api_base: "os.environ/LLM_BACKEND_URL"
      api_key: "os.environ/LLM_BACKEND_API_KEY"

  # === 方案 B：MiniMax M2.7 ===
  - model_name: claude-sonnet-4-5-20250929
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"

  - model_name: claude-haiku-4-5-20251001
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"

  # === 兜底 fallback：sonnet 优先 NVIDIA，失败回退 GLM ===
  - model_name: claude-sonnet-fallback
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"
    fallbacks:
      - model_name: claude-opus-4-6   # 触发 NVIDIA 限速时回落到 GLM-5.1

litellm_settings:
  drop_params: true
  num_retries: 3
  request_timeout: 600
```

`.env` 里加一行：

```bash
NVIDIA_API_KEY=nvapi-你的key
```

重新部署：

```bash
uvx --with python-dotenv modal deploy litellm_proxy.py
```

#### Step B3 — Claude Code 配置不变

`ANTHROPIC_BASE_URL` 还是那个 Modal proxy URL。**Claude Code 选 sonnet 时走 M2.7，选 opus 时走 GLM-5.1，选 haiku 时也走 M2.7**——你在 IDE 里随便切。

#### 不想用 LiteLLM 的极简版

如果你只想试 M2.7 不想搭 proxy，**可以直接用支持 OpenAI 兼容 endpoint 的客户端**，比如：

- **OpenCode**（CLI）：`opencode` 配置里直接填 NVIDIA base_url
- **Cline / Roo Code**（VS Code）：插件设置里选 OpenAI Compatible，填 base_url + key
- **Cursor / Continue**：自定义模型时填 NVIDIA endpoint

但要用 **Claude Code** 的话，因为它只吃 Anthropic Messages API，**绕不开 LiteLLM 这一层翻译**——这是 Anthropic 官方文档明确写的硬约束。

---

## 顶层设计：两个方案怎么搭配最香

我推荐你**两个都部署**，在同一个 LiteLLM 配置里做路由：

| Claude Code 模型选择 | 实际路由到 | 为什么 |
|---|---|---|
| `claude-haiku-4-5-*`（快任务） | NVIDIA M2.7 | 40 RPM 够用，不消耗 GLM 单并发 |
| `claude-sonnet-4-5-*`（日常） | NVIDIA M2.7 → GLM 兜底 | 日常请求走 NVIDIA，限速时回落 |
| `claude-opus-4-6`（长程任务） | Modal GLM-5.1 | 192K 上下文 + Opus 级别能力 |

这样的好处：

- ✅ **完全合规**：没有用 Anthropic Token，不踩 ToS
- ✅ **零月费**：4 月 30 日前两边都白嫖；4 月 30 日后 NVIDIA 还能撑着
- ✅ **能力分层**：M2.7 跑量、GLM-5.1 跑质，都是开源 MIT/Apache
- ✅ **故障兜底**：一边挂了另一边接住，比单一 Anthropic 还稳

---

## 几个真实坑位（双方案通用）

### 1. 别把 proxy URL 公开

部署在 Modal 上的 `*.modal.run` URL **默认公网可访问**——任何人拿到这个 URL 都能用你的 Modal/NVIDIA Key 调模型。

**加鉴权**：在 `litellm_config.yaml` 加：

```yaml
general_settings:
  master_key: sk-我自己设的强密码
```

然后 Claude Code 端 `export ANTHROPIC_AUTH_TOKEN=sk-我自己设的强密码`。

### 2. LiteLLM 安全雷点

PyPI 上的 `litellm 1.82.7` 和 `1.82.8` 曾被注入凭据窃取恶意代码（[BerriAI/litellm#24518](https://github.com/BerriAI/litellm/issues/24518)，Anthropic 官方也警告）。**Modal 方案因为用的是官方 Docker 镜像 `docker.litellm.ai/berriai/litellm:main-latest`，避开了这个雷**。本地装的同学务必检查版本。

### 3. NVIDIA NIM 的 40 RPM 是怎么算的

是**整个账号下所有模型加起来 40 RPM**，不是单个模型 40。如果你同时调 M2.7 + Llama-4 + Qwen3.5，会一起共享这 40。Claude Code 一般场景里达不到，但跑批量 agent 任务时要注意。

### 4. NVIDIA NIM 的 credit 消耗规则

1000 免费 credits 不是按 token 算，是**按请求次数 + 模型规模加权**。M2.7 这种 230B 模型大概 1 credit/请求 ~ 5 credits/请求（看输入长度）。日常编程一天大概消耗 50-100 credits，**可以撑两到三周**。用完后可以申请到 5000，[官方表单在这](https://forums.developer.nvidia.com/c/api-developer/api-access)。

### 5. GLM-5.1 单并发的体感

你一个对话窗口同时只能发一个请求。两个 Claude Code 窗口同时让 GLM-5.1 跑任务，第二个会被阻塞或报错。**所以 fallback 设成 NVIDIA 主、GLM 后备很合理**。

### 6. 4 月 30 日之后的兜底

- **如果 Modal 不续期**：把 sonnet/opus 全部路由到 NVIDIA M2.7，照样能用
- **如果 NVIDIA credits 用完**：去 z.ai 订 GLM Coding Plan（最低 $3/月起）
- **彻底独立**：用 Unsloth 把 GLM-5.1 量化到 220GB，自己 4×A100 跑

---

## 配套资源（全部一手源）

**方案 A · Modal GLM-5.1**

- 官方 Claude Code 模板 ⭐：<https://github.com/modal-projects/modal-jazz/tree/main/frontends/claude>
- Modal GLM-5.1 endpoint：<https://modal.com/glm-5-endpoint>
- Modal 介绍博客：<https://modal.com/blog/try-glm-5>
- GLM-5.1 GitHub：<https://github.com/zai-org/GLM-5>

**方案 B · NVIDIA NIM Minimax M2.7**

- NVIDIA NIM 模型卡 ⭐：<https://build.nvidia.com/minimaxai/minimax-m2.7>
- NVIDIA 官方技术博客：<https://developer.nvidia.com/blog/minimax-m2-7-advances-scalable-agentic-workflows-on-nvidia-platforms-for-complex-ai-applications/>
- API 参考：<https://docs.api.nvidia.com/nim/reference/minimaxai-minimax-m2.7>
- MiniMax M2.7 GitHub：<https://github.com/MiniMax-AI/MiniMax-M2.7>
- HF 权重：<https://huggingface.co/MiniMaxAI/MiniMax-M2.7>

**通用**

- Claude Code Gateway 官方文档：<https://code.claude.com/docs/en/llm-gateway>
- LiteLLM NVIDIA NIM provider：<https://docs.litellm.ai/docs/providers/nvidia_nim>
- Anthropic 政策原文（2026-02 OAuth ban）：<https://docs.claude.com/en/legal/consumer-terms>

---

## 写在最后

Anthropic 这次封禁 OAuth 第三方使用，本质是**把订阅模式和 API 模式硬切开**——以前那种"$20 Max 当一万块 API 用"的灰色地带没了，他们要的是要么按 token 付钱、要么离开。

但**模型层面的开源浪潮反而被这一刀逼出来了**：

- **智谱**：MIT 开源 GLM-5.1，对标 Opus，免费托管两周
- **MiniMax**：Apache 2.0 开源 M2.7，挂上 NVIDIA 永久免费层
- **NVIDIA**：用免费推理把开发者从云大厂拉到自己平台

对开发者来说，**你完全可以做到月费 $0，能力 ≥ Anthropic 订阅 80%**。配置成本就是今晚 30 分钟（如果你已经装过 Modal CLI 就 15 分钟），回报是免费跑「开源版 Opus + 开源版 Sonnet」当编程主力。

> 抓手在这里：今晚 30 分钟把双方案都部署上，明天用一周对比 Anthropic 账单和体感。数据出来你自己就知道这事儿值不值得长期投入。

> 最坏的情况：4 月 30 日 Modal 不续期 + NVIDIA credits 用完——你也已经摸清了「开源模型 + LiteLLM + 自部署」这条链路，下一个免费窗口出现时（一定会有），你 5 分钟就能切过去。这才是这篇真正的价值。

---

*— AI-Tracker 2026-04-16*
