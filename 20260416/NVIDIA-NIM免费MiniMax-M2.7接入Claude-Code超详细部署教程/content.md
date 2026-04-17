# 老黄请客！NVIDIA NIM 免费 MiniMax M2.7 接入 Claude Code 超详细部署教程

![老黄请客！白嫖 MiniMax M2.7 封面](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/nvidia-mm27-cover.png)

> 发布日期：2026-04-16  
> 标签：#ClaudeCode #MinimaxM2.7 #NVIDIA-NIM #LiteLLM #AI编程  
> 关键时间窗：注册即送 **1000 推理 credits**（可申请到 5000）·**40 RPM** rate limit · 模型上线 2026-04-11  
> 适用人群：被 Claude Code 账单割肉的开发者、需要 40 RPM 中等并发的人、想白嫖 230B MoE 大模型的人

![NVIDIA × MiniMax M2.7 release](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/nvidia-mm-release.png)

---

## 0. 你为什么需要这一篇

2026 年 2 月，Anthropic 在 ToS 里加了一条**毫无回旋余地的话**：

> "The use of OAuth tokens obtained via Claude Free, Pro, or Max accounts in any other product, tool, or service is not permitted."

翻译：**Claude Free / Pro / Max 订阅拿到的 OAuth Token，不允许用在 Claude Code 之外的任何工具上**。OpenClaw / OpenCode / Cline 借用 Max 订阅认证一夜之间 401，过去半年最热的"省钱姿势"全部失效。

要么真金白银按 token 付钱，要么——**找一条合法的免费替代路**。这篇就是另一条：**老黄（NVIDIA）+ MiniMax M2.7（230B MoE）的永久免费方案**。

> 📌 想看 Modal × GLM-5.1（**对标 Opus 4.5、限时免费到 04-30**）的姊妹篇？请看 → `../Modal免费GLM-5.1接入Claude-Code超详细部署教程/content.md`

---

## 1. 这次到底白嫖了什么

老黄（NVIDIA）也在搞**免费 AI 推理云**了——`build.nvidia.com` 平台聚合了一堆 NIM（NVIDIA Inference Microservice）模型，**注册即送 1000 推理 credits**，可向官方申请扩容到 5000，**40 RPM**（请求/分钟）rate limit。MiniMax M2.7 是 2026-04-11 上线的最新模型。

| 项目 | 数值 |
|---|---|
| **模型** | `minimaxai/minimax-m2.7` |
| **总参数** | 230B（MoE） |
| **激活参数** | 10B（256 个 local experts，每 token 激活 8 个） |
| **上下文窗口** | 204,800 tokens（比 GLM-5.1 还大一点） |
| **吞吐** | ~60 tok/s |
| **rate limit** | **40 RPM**（整个账号下所有 NIM 模型加起来） |
| **免费额度** | 注册送 1000 credits（可申请到 5000） |
| **许可证** | Apache 2.0 开源 |
| **官方定位** | 复杂软件工程 + agentic 工具调用 + 办公生产力 |
| **API 格式** | OpenAI 兼容（`/v1/chat/completions`） |
| **入口** | <https://build.nvidia.com/minimaxai/minimax-m2.7> |

**MiniMax M2.7 在 MiniMax 官方 Agent 里的样子**（M2.7 + Gemini 3.0 Pro 是当前可选的两个最强模型）：

![MiniMax Agent with M2.7](https://cdn.jsdelivr.net/gh/twwch/images/AI-Tracker/images/2026/04/minimax-agent-m27.png)

---

## 2. 接入原理（不要跳过这段）

Claude Code **只吃三种 API 格式**（[官方文档明确写](https://code.claude.com/docs/en/llm-gateway)）：
1. Anthropic Messages（`/v1/messages`）
2. Bedrock InvokeModel
3. Vertex rawPredict

**NVIDIA NIM 的 endpoint 是 OpenAI 兼容**（`/v1/chat/completions`），**直接连不通**。所以中间必须有一层翻译——就是 LiteLLM。

链路就是：

```
你的电脑 (Claude Code)
    ↓ Anthropic /v1/messages
LiteLLM Proxy（本地起 docker 或部署到 Modal/任意 PaaS）← 这层做协议翻译
    ↓ OpenAI /chat/completions
NVIDIA NIM (https://integrate.api.nvidia.com/v1)
    ↓ 路由
MiniMax-M2.7 (minimaxai/minimax-m2.7)
```

> **如果你已经按 Modal × GLM-5.1 那篇部署过 LiteLLM proxy，本篇 99% 的内容你已经搞定**——只需要在 `litellm_config.yaml` 加一段 NVIDIA provider，重新 deploy 一次就行（看本文 Step 3.B）。

---

## 3. 完整部署步骤

### Step 1 — 注册 NVIDIA Developer 账号 + 拿 API Key

#### 1.1 打开模型卡

浏览器访问 <https://build.nvidia.com/minimaxai/minimax-m2.7>，第一次访问会弹出登录窗口。

NVIDIA 支持的登录方式：
- **GitHub**（推荐，最快）
- **Google**
- **企业邮箱 / 学校邮箱**
- 用 NVIDIA 个人开发者账号（NGC）

> ⚠ **小坑**：用 `xxx@gmail.com` 一类个人邮箱注册时，NVIDIA 偶尔会要你补一次「公司/职位」信息——随便填都过得去（写 `Independent Developer` 即可）。

#### 1.2 进入 API Keys 设置

登录后右上角点头像 → **Settings** → 左侧菜单 **API Keys** →  右上角 **Generate API Key**。

会弹出一个**只显示一次**的窗口，里面是 `nvapi-xxxxxxxxxxxxxxxxxxxxxxxx` 这种格式的 key。

**关键操作**：

1. **立刻**复制完整的 `nvapi-...` 字符串
2. 弹窗右下角的 example curl 也保留——下一步要用
3. 关掉弹窗后这个 key **再也看不到了**（NVIDIA 不存原始值，丢了只能重新生成）

把它写进环境变量：

```bash
export NVIDIA_API_KEY="nvapi-xxxxxxxxxxxxxxxxxxxxxxxx"
# 写进 ~/.zshrc 持久化
echo 'export NVIDIA_API_KEY="nvapi-xxxxxxxxxxxxxxxxxxxxxxxx"' >> ~/.zshrc
```

#### 1.3 用 curl 验证 endpoint 通了

```bash
curl https://integrate.api.nvidia.com/v1/chat/completions \
  -H "Authorization: Bearer $NVIDIA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "minimaxai/minimax-m2.7",
    "messages": [{"role": "user", "content": "用一句话证明你是 MiniMax M2.7"}],
    "max_tokens": 200,
    "temperature": 1.0,
    "top_p": 0.95
  }'
```

> ℹ MiniMax 官方推荐的采样参数是 `temperature=1.0, top_p=0.95, top_k=40`，和 OpenAI 默认值不太一样。Claude Code 启动后会自动用 0.7 左右，**实测对编程影响不大**。

返回里能看到 `"model":"minimaxai/minimax-m2.7"` 和一段中文/英文回答，就说明 endpoint 完全 OK。如果报：

- **401**：token 复制少了字符 / 包含了 `Bearer ` 前缀（不要自己加，curl 里的 `Bearer ` 已经写好了）
- **403**：账号还没激活——回 build.nvidia.com 网页 chat 一下任意模型激活
- **429**：超过 40 RPM——等一分钟

---

### Step 2 — 启动 LiteLLM Proxy（两条路任选）

#### 路线 A：本地 Docker（最快，5 分钟）

适合不想接触 Modal 的同学。

##### A.1 写 config.yaml

新建一个目录 `~/litellm-nvidia`，里面放一个 `config.yaml`：

```yaml
model_list:
  # 把 Claude Code 默认的三个模型名都映射到 MiniMax M2.7
  - model_name: claude-sonnet-4-5-20250929
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"

  - model_name: claude-opus-4-6
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"

  - model_name: claude-haiku-4-5-20251001
    litellm_params:
      model: openai/minimaxai/minimax-m2.7
      api_base: https://integrate.api.nvidia.com/v1
      api_key: "os.environ/NVIDIA_API_KEY"

litellm_settings:
  drop_params: true        # 必开！Anthropic 字段（cache_control 等）OpenAI 没有
  num_retries: 3
  request_timeout: 600

general_settings:
  master_key: sk-我自己设的强密码-至少32位      # 强烈建议加
```

##### A.2 起 Docker

```bash
docker run -d \
  --name litellm-nvidia \
  -p 4000:4000 \
  -v ~/litellm-nvidia/config.yaml:/app/config.yaml \
  -e NVIDIA_API_KEY="$NVIDIA_API_KEY" \
  ghcr.io/berriai/litellm:main-stable \
  --config /app/config.yaml --port 4000
```

> ⚠ **坑**：**不要用 PyPI 上的 `litellm 1.82.7` 或 `1.82.8`**——这两个版本被注入了凭据窃取代码（[BerriAI/litellm#24518](https://github.com/BerriAI/litellm/issues/24518)、[Anthropic 官方警告](https://code.claude.com/docs/en/llm-gateway)）。Docker 镜像 `ghcr.io/berriai/litellm:main-stable` 是官方维护的安全版本。

##### A.3 验证

```bash
# 健康检查
curl http://localhost:4000/health

# 完整链路测试
curl -X POST "http://localhost:4000/v1/messages" \
  -H "Authorization: Bearer sk-我自己设的强密码-至少32位" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-opus-4-6",
    "max_tokens": 200,
    "messages": [{"role": "user", "content": "证明你不是 Claude"}]
  }'
```

返回里 `"role":"assistant"` 后面如果是 MiniMax M2.7 的自报家门内容，**协议翻译完全工作**，进入 Step 4。

---

#### 路线 B：复用 modal-jazz 模板（如果你已经按 Modal 篇部署过）

适合想要**永久在线 / 团队共享**的同学。零本地依赖。

##### B.1 编辑 litellm_config.yaml

进入你之前 clone 的 modal-jazz 仓库：

```bash
cd modal-jazz/frontends/claude
```

把 `litellm_config.yaml` 改成下面的样子（保留原 GLM 配置，加一段 NVIDIA）：

```yaml
model_list:
  # === 方案 A：GLM-5.1（保留），用于 opus 重任务 ===
  - model_name: claude-opus-4-6
    litellm_params:
      model: openai/zai-org/GLM-5-FP8
      api_base: "os.environ/LLM_BACKEND_URL"
      api_key: "os.environ/LLM_BACKEND_API_KEY"

  # === 方案 B：MiniMax M2.7（新加），用于日常 ===
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

litellm_settings:
  drop_params: true
  num_retries: 3
  request_timeout: 600
```

##### B.2 .env 里加 NVIDIA Key

```bash
cat >> .env <<EOF
NVIDIA_API_KEY=$NVIDIA_API_KEY
EOF
```

##### B.3 重新 deploy

```bash
uvx --with python-dotenv modal deploy litellm_proxy.py
```

会输出 URL：`https://your-org--litellm-proxy-serve.modal.run`，下一步用。

---

### Step 3 — 配置 Claude Code 指向 Proxy

#### 路线 A 用户（本地 Docker）

```bash
export ANTHROPIC_BASE_URL="http://localhost:4000"
export ANTHROPIC_AUTH_TOKEN="sk-我自己设的强密码-至少32位"
claude
```

#### 路线 B 用户（Modal Serverless）

```bash
export ANTHROPIC_BASE_URL="https://your-org--litellm-proxy-serve.modal.run"
claude
```

进去之后试一个：

```
> 用 TypeScript 写一个 Express 服务，包含 JWT 中间件、Zod 校验和单元测试
```

工具调用、文件编辑、子代理（subagent）、MCP plugins、Skills、Hooks——**Claude Code 整个生态都正常工作**，因为 MiniMax M2.7 完全支持 function calling。

#### 永久生效

```bash
echo 'export ANTHROPIC_BASE_URL="http://localhost:4000"' >> ~/.zshrc
echo 'export ANTHROPIC_AUTH_TOKEN="sk-我自己设的强密码-至少32位"' >> ~/.zshrc
source ~/.zshrc
```

#### 推荐别名

```bash
cat >> ~/.zshrc <<'EOF'
alias claude-mm='ANTHROPIC_BASE_URL="http://localhost:4000" ANTHROPIC_AUTH_TOKEN="sk-我自己设的强密码-至少32位" claude'
alias claude-anthropic='ANTHROPIC_BASE_URL="" ANTHROPIC_AUTH_TOKEN="" claude'
EOF
source ~/.zshrc
```

---

## 4. （可选）不用 Claude Code，用其他客户端

如果你日常用的是 Cline / Cursor / Continue / Kilo Code 这类**直接支持 OpenAI 兼容 endpoint** 的工具，**完全不用部署 LiteLLM**——直接填 NVIDIA 原生 URL：

| 字段 | 填什么 |
|---|---|
| API Provider | `OpenAI Compatible` |
| Base URL | `https://integrate.api.nvidia.com/v1` |
| API Key | 你的 `nvapi-xxxxx` |
| Model | `minimaxai/minimax-m2.7` |

这种姿势**省掉了协议翻译那一层**，延迟更低、出错点更少。

---

## 5. 真实坑位排雷

### ❶ 40 RPM 是整个账号共享的

**不是单个模型 40，是你账号下所有 NIM 模型加起来 40**。如果你同时在 Cursor 里调 M2.7、在 Cline 里调 Llama-4、在 Claude Code 里调 Qwen3.5，会一起共享这 40。日常编程一般达不到，但跑批量 agent 任务时要注意。

LiteLLM 里加 fallback 可以缓解：

```yaml
- model_name: claude-sonnet-fallback
  litellm_params:
    model: openai/minimaxai/minimax-m2.7
    api_base: https://integrate.api.nvidia.com/v1
    api_key: "os.environ/NVIDIA_API_KEY"
  fallbacks:
    - model_name: claude-opus-4-6   # NVIDIA 限速时回落到 GLM-5.1（如果你也部署了）
```

### ❷ Credit 怎么算？1000 能用多久？

1000 免费 credits **不是按 token 算，是按请求次数 + 模型规模加权**。M2.7 这种 230B 模型大概每个请求消耗 **1~5 credits**（看输入长度）。日常编程一天大概 50–100 credits，**可以撑两到三周**。

用完后两条路：
- 申请扩容到 5000：[NVIDIA Developer Forum 申请帖](https://forums.developer.nvidia.com/c/api-developer/api-access)
- 公司 / 团队用：买 NVIDIA AI Enterprise（贵，年订阅起步）

### ❸ `temperature=1.0` 还是 `0.7`？

MiniMax 官方推荐 `temperature=1.0, top_p=0.95, top_k=40`，但 Claude Code 默认会用 `temperature=1` 加 `top_p=1`。**实测在编程场景下两套参数差异不大**——M2.7 训练时本来就是为这套采样调的。如果你想严格按官方推荐，可以在 LiteLLM config 里强制：

```yaml
- model_name: claude-sonnet-4-5-20250929
  litellm_params:
    model: openai/minimaxai/minimax-m2.7
    api_base: https://integrate.api.nvidia.com/v1
    api_key: "os.environ/NVIDIA_API_KEY"
    temperature: 1.0
    top_p: 0.95
```

### ❹ Vision 不可用

NIM 上的 M2.7 endpoint **只开了文本**——传图会直接 400。如果你要做 multimodal，用 NVIDIA 上其他 VLM 模型（如 `meta/llama-4-maverick-17b-vision`）。

### ❺ 部署的 URL 默认公网可访问

如果走 Modal 部署（路线 B），`*.modal.run` URL 任何人拿到都能用——会消耗你的 NVIDIA credits。**强烈推荐加 master_key 鉴权**（已在配置里加好）。

### ❻ Claude Code 切模型会发生什么？

我们的 LiteLLM 把 `haiku/sonnet/opus` 全都映射到 M2.7，所以你在 Claude Code 里 `/model` 切来切去，**底层都是 M2.7**。如果你同时配了 GLM-5.1（参考姊妹篇），可以做更细致的路由：

| Claude Code 选 | 实际路由到 | 为什么 |
|---|---|---|
| `claude-haiku-*` | NVIDIA M2.7 | 40 RPM 够日常快任务 |
| `claude-sonnet-*` | NVIDIA M2.7 → GLM 兜底 | 限速时回落 |
| `claude-opus-4-6` | Modal GLM-5.1 | 192K 上下文 + Opus 级别 |

---

## 6. 编码体感（HN + Reddit + 自测）

- **优点**：
  - **40 RPM 比 GLM-5.1 单并发实用得多**——可以并行跑多个 agent
  - 204K 上下文（比 GLM-5.1 还大 12K）
  - 工具调用稳定，agentic workflow 流畅
  - Apache 2.0，商用零顾虑
- **缺点**：
  - 单题深度推理不如 GLM-5.1 / Opus
  - 中文质量一般（MiniMax 主战场是中文，但 NIM 上的版本似乎是英文优化的）
  - 需要 NVIDIA Developer 账号（多一道注册）
- **盲区**：
  - 不开放 fine-tune / LoRA
  - 不能控制 GPU 类型（NVIDIA 自己分配）
  - 长文本输入有时会被截断到 128K（具体阈值 NVIDIA 没文档化）

---

## 7. 配套资源（全部一手源）

- **NVIDIA NIM 模型卡** ⭐：<https://build.nvidia.com/minimaxai/minimax-m2.7>
- **NVIDIA 官方技术博客**：<https://developer.nvidia.com/blog/minimax-m2-7-advances-scalable-agentic-workflows-on-nvidia-platforms-for-complex-ai-applications/>
- **API 参考**：<https://docs.api.nvidia.com/nim/reference/minimaxai-minimax-m2.7>
- **MiniMax M2.7 GitHub**：<https://github.com/MiniMax-AI/MiniMax-M2.7>
- **HF 权重**：<https://huggingface.co/MiniMaxAI/MiniMax-M2.7>
- **MiniMax M2.7 本地运行（Unsloth 量化版）**：<https://unsloth.ai/docs/models/minimax-m27>
- **MiniMax 官方 Coding Agent 文档**：<https://platform.minimax.io/docs/guides/text-ai-coding-tools>
- **Claude Code Gateway 官方文档**：<https://code.claude.com/docs/en/llm-gateway>
- **LiteLLM NVIDIA NIM provider**：<https://docs.litellm.ai/docs/providers/nvidia_nim>
- **API Key 申请页**：<https://build.nvidia.com/settings/api-keys>

> 📌 想看 Modal × GLM-5.1（**对标 Opus 4.5、限时免费到 04-30**）的姊妹篇？请看 → `../Modal免费GLM-5.1接入Claude-Code超详细部署教程/content.md`  
> 强烈建议两个都部署，在 LiteLLM 里做 fallback：M2.7 跑日常、GLM-5.1 跑长程任务——这就是「免费版 Sonnet + 免费版 Opus」的双保险架构。

---

## 8. 写在最后

老黄这次释放免费 NIM credits，**本质上是在抢 Anthropic / OpenAI 的开发者心智**：

- 你免费用 NIM 上的 M2.7、Llama-4、Qwen3.5、Mistral，习惯了 NVIDIA 的 SDK 和 toolchain
- 等你公司要部署到生产环境，自然就会买 **NVIDIA AI Enterprise**（NIM 的付费版，按 GPU 起步）
- NVIDIA 卖的不是模型，是「**模型 + 高效推理软件栈 + 标准化部署接口**」这一整套——这才是它的护城河

对开发者来说，**这就是白嫖窗口**。从 4 月 16 日开始，1000 credits 可以撑两到三周；用完后申请扩容到 5000 还能再撑一两个月。配置成本 5 分钟，回报是免费跑一台 230B MoE 当编程主力。

> **抓手在这里**：今晚 5 分钟跑通本地 Docker 路线，明天用一周对比 Anthropic 账单和体感。如果你已经按 Modal 篇部署过，那么这一篇只需要在 yaml 里加几行配置 + 一次 `modal deploy`。最坏情况——M2.7 不再免费，你也已经摸清「OpenAI 兼容 + LiteLLM + Claude Code」整条链路，下次任何免费 NIM 模型上线（NVIDIA 几乎每月都上新），5 分钟切过去。

---

*— AI-Tracker 2026-04-16 · NVIDIA × MiniMax M2.7 详细部署篇*
