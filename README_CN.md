# Agnes2Opencode

**Agnes AI 的 OpenAI 兼容代理服务器** — 使用任意 OpenAI 客户端访问 Agnes 模型，内置精美仪表板，自动配置 opencode。零外部依赖。

[![Node.js](https://img.shields.io/badge/Node.js-v14%2B-green)](https://nodejs.org) [![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE) [![零依赖](https://img.shields.io/badge/依赖-零-brightgreen)]()

🇺🇸 [English README](README.md)

---

<img width="1206" height="683" alt="仪表板界面" src="https://github.com/user-attachments/assets/96f3e2d1-c566-4926-9fbb-e173f6c8652b" />

<img width="638" height="159" alt="opencode 集成" src="https://github.com/user-attachments/assets/1a963114-d3c9-4e3c-9ac6-9516957b793e" />

<img width="1301" height="866" alt="计划状态与密钥管理" src="https://github.com/user-attachments/assets/c2dd6e32-9f06-464c-ae68-c7d7f87c91c7" />

---

## 这是什么？

Agnes2Opencode 是一个运行在本地的代理服务器，介于你的 AI 工具和 Agnes AI API 之间。它提供标准的 OpenAI 兼容接口（`/v1/chat/completions`），让你可以用**任何 OpenAI 客户端** — 包括 opencode、Cursor、Continue 或你自己的代码 — 接入 Agnes AI 模型。

**适合哪些人使用？**
- 想要将 Agnes AI 作为 opencode 提供商的用户
- 使用 OpenAI SDK 开发应用、想免费接入 Agnes AI 的开发者
- 任何想通过仪表板管理 Agnes AI 密钥和用量的人

---

## 功能特性

- **OpenAI 兼容 API** — 标准的 `/v1/chat/completions` 和 `/v1/models` 接口
- **流式响应支持** — SSE 实时流式输出
- **自动配置 opencode** — 启动时自动将 Agnes 写入 opencode 提供商配置
- **精美仪表板** — 液态玻璃 UI，含模型开关、密钥管理、计划状态、缓存统计
- **多密钥轮换** — 在多个 API 密钥之间分发请求，支持会话粘性跟踪
- **平台登录** — 登录 Agnes AI 账号，查看订阅状态和用量
- **响应缓存** — 非流式响应的 LRU 缓存（可配置 TTL 和大小）
- **自动重试** — 指数退避自动重试上游瞬时错误
- **AI 壁纸** — 通过 AI 生成仪表板背景图或使用必应每日图片
- **工具模式规范化** — 转发前自动修复 `$ref`/`$defs` 工具模式
- **模型映射** — 自动将旧版模型 ID 转换为当前对应版本
- **测试模式** — 返回模拟响应，不消耗 API 额度
- **零依赖** — 无需 `npm install`，纯 Node.js 内置模块

---

## 使用教程：从零开始

> **快速开始：** 获取 Agnes AI 密钥 → 复制 `config.example.json` 为 `config.json` → 填入密钥 → `node proxy.js` → 打开 http://localhost:8080

按照以下步骤，快速搭建完整的使用环境。

### 第一步 — 获取 Agnes AI API 密钥

1. 前往 [agnes-ai.com](https://agnes-ai.com) 注册免费账号
2. 进入 API 密钥管理页面
3. 复制你的密钥 — 格式为 `sk-` 开头

> **免费额度说明：** Agnes AI 的基础模型（如 `agnes-2.0-flash`）永久免费，无需绑定信用卡。速率限制根据服务器实时负载动态调整，而非固定月度配额。

---

### 第二步 — 下载代理程序

**方式一：Git 克隆**
```bash
git clone https://github.com/danissimo888/Agnes2Opencode.git
cd Agnes2Opencode
```

**方式二：下载 ZIP**

在 GitHub 页面点击 **Code → Download ZIP**，解压后进入文件夹。

---

### 第三步 — 填入你的 API 密钥

复制示例配置文件并填入你的密钥：

```bash
# macOS / Linux
cp .config/config.example.json .config/config.json

# Windows 命令提示符
copy .config\config.example.json .config\config.json
```

然后打开 `.config/config.json`，替换占位符：

```json
{
  "API_KEY": "sk-你的-agnes-api-密钥"
}
```

> `config.json` 已在 `.gitignore` 中 — 不会被意外提交。

**替代方案 — 使用环境变量（无需修改文件）：**

```bash
# Windows 命令提示符
set AGNES_API_KEY=sk-你的密钥
node proxy.js

# Windows PowerShell
$env:AGNES_API_KEY="sk-你的密钥"
node proxy.js

# macOS / Linux
AGNES_API_KEY=sk-你的密钥 node proxy.js
```

---

### 第四步 — 启动代理

**Windows（最简单）：**
双击 `start.cmd` — 自动检测 Bun 或 Node.js，自动释放 8080 端口，启动代理。

**任意系统：**
```bash
node proxy.js
```

**使用 Bun（启动更快）：**
```bash
bun proxy.js
```

启动成功后你会看到：
```
[Agnes Proxy] Listening on http://127.0.0.1:8080
[Agnes Proxy] Dashboard: http://localhost:8080
```

---

### 第五步 — 打开仪表板

在浏览器访问 **http://localhost:8080**。

仪表板功能：
- 查看订阅计划状态和用量
- 添加、编辑或删除 API 密钥
- 开启/关闭可用模型
- 监控缓存命中率和密钥健康状态
- 登录 Agnes AI 平台账号
- 切换壁纸模式（无 / 必应 / AI 生成）

---

### 第六步 — 连接到 opencode

1. 确保代理已运行（第四步）
2. 重启 opencode
3. 在提供商选择中选择 **agnes**

代理在启动时会自动将 Agnes 提供商配置写入 `~/.config/opencode/opencode.json`。首次写入前会自动创建备份。无需手动编辑 JSON 文件。

---

### 第七步 — 接入任意 OpenAI 客户端

将 OpenAI 客户端指向 `http://localhost:8080/v1`：

**JavaScript / Node.js：**
```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  baseURL: 'http://localhost:8080/v1',
  apiKey: 'not-needed'  // 代理负责鉴权
});

const response = await client.chat.completions.create({
  model: 'agnes-2.0-flash',
  messages: [{ role: 'user', content: '你好！' }]
});

console.log(response.choices[0].message.content);
```

**Python：**
```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8080/v1",
    api_key="not-needed"
)

response = client.chat.completions.create(
    model="agnes-2.0-flash",
    messages=[{"role": "user", "content": "你好！"}]
)

print(response.choices[0].message.content)
```

**cURL：**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "agnes-2.0-flash",
    "messages": [{"role": "user", "content": "你好！"}]
  }'
```

---

## 可用模型

| 模型 ID | 类型 | 免费 | 上下文 |
|---------|------|------|--------|
| `agnes-2.0-flash` | 文本 | ✅ 是 | 256K |
| `agnes-1.5-flash` | 文本 | ✅ 是 | 256K |
| `agnes-image-2.0-flash` | 图像 | ✅ 是 | — |
| `agnes-image-2.1-flash` | 图像 | ✅ 是 | — |
| `agnes-video-v2.0` | 视频 | ✅ 是 | — |

模型列表在启动时从 Agnes AI 动态获取（缓存 5 分钟）。旧版模型 ID（如 `sapiens-ai/agnes-1.5-pro`）自动映射到当前版本。

---

## 免费额度说明

- **无需信用卡** — 基础模型永久免费
- **动态速率限制** — 没有固定月度配额，平台根据实时服务器负载动态限流。高峰期速度可能降低，以优先保障付费流量
- **内置重试** — 代理在瞬时错误时自动指数退避重试（5s → 10s → 15s，最多 3 次）

如果你觉得这个服务有价值，欢迎[订阅付费计划](https://platform.agnes-ai.com/subscribe/subscription?from=website)来支持平台运营。

---

## 配置项说明

编辑 `.config/config.json` 或设置对应的环境变量：

| 配置项 | 环境变量 | 说明 | 默认值 |
|--------|---------|------|--------|
| `LISTEN_ADDR` | `LISTEN_ADDR` | 代理监听地址 | `127.0.0.1:8080` |
| `UPSTREAM_BASE_URL` | `UPSTREAM_BASE_URL` | Agnes API 地址 | `https://apihub.agnes-ai.com` |
| `API_KEY` | `AGNES_API_KEY` | 你的 Agnes AI API 密钥 | — |
| `REQUEST_TIMEOUT` | `REQUEST_TIMEOUT` | 上游请求超时时间 | `15m` |
| `API_KEYS` | `API_KEYS` | 代理鉴权密钥（空 = 开放访问） | `[]` |
| `ENABLED_MODELS` | — | 对客户端可见的模型 | 全部 |
| `CACHE_TTL` | `CACHE_TTL` | 响应缓存存活时间 | `60s` |
| `CACHE_MAX_SIZE` | `CACHE_MAX_SIZE` | 最大缓存响应数量 | `100` |
| `CACHE_ENABLED` | `CACHE_ENABLED` | 是否启用响应缓存 | `true` |
| `WALLPAPER_MODE` | — | `none`、`bing` 或 `ai` | `bing` |
| `WALLPAPER_PROMPT` | — | AI 壁纸生成提示词 | `realistic vibrant colorful mountain range landscape` |
| `TEST_MODE` | `TEST_MODE` | 返回模拟响应（不消耗 API） | `false` |

---

## 多密钥配置

添加多个 API 密钥，将请求分发到不同账号：

```json
{
  "TOKENS": [
    { "name": "密钥1", "token": "sk-你的第一个密钥" },
    { "name": "密钥2", "token": "sk-你的第二个密钥" }
  ]
}
```

每个密钥还可以存储平台凭证，用于自动登录：

```json
{
  "TOKENS": [
    {
      "name": "工作账号",
      "token": "sk-你的密钥",
      "platformUsername": "你@example.com",
      "platformPassword": "你的密码"
    }
  ]
}
```

**轮换机制说明：** 每个会话通过首条用户消息的 MD5 指纹识别。同一会话的后续请求会固定到同一密钥。新会话按轮询方式分配密钥，并在消息中标注 `[密钥名|会话N]` 便于追踪。

也可以直接在 **仪表板 → 管理密钥** 面板中操作，无需手动编辑 JSON。

---

## 访问控制（代理鉴权密钥）

默认情况下代理是开放的。如需要限制访问，设置 `API_KEYS`：

```json
{
  "API_KEYS": ["我的密钥1", "我的密钥2"]
}
```

客户端请求时需要携带密钥：

```bash
curl -H "Authorization: Bearer 我的密钥1" http://localhost:8080/v1/models
# 或者
curl -H "x-api-key: 我的密钥1" http://localhost:8080/v1/models
```

---

## API 接口

### 核心接口

| 方法 | 路径 | 说明 |
|------|------|------|
| `GET` | `/healthz` | 健康检查 — API 密钥状态、运行时间、缓存统计 |
| `GET` | `/v1/models` | OpenAI 格式模型列表 |
| `POST` | `/v1/chat/completions` | 对话补全（流式、缓存、重试） |

### 管理接口

| 方法 | 路径 | 说明 |
|------|------|------|
| `GET/POST` | `/api/config` | 读取 / 写入代理配置 |
| `GET` | `/api/validate` | 验证 API 密钥是否有效 |
| `GET` | `/api/models` | 带元数据的模型列表 |
| `GET` | `/api/bg` | 仪表板壁纸图片 |
| `POST` | `/api/generate-image` | 生成 AI 壁纸 |
| `GET/POST` | `/api/keys` | 多密钥增删改查 |
| `GET` | `/api/account` | 平台账号信息 |
| `GET` | `/api/step-plan-status` | 订阅计划和用量窗口 |
| `POST` | `/api/login` | 平台账号登录 |
| `POST` | `/api/logout` | 清除平台会话 |
| `GET/DELETE` | `/api/cache` | 查看 / 清除响应缓存 |

---

## 常见问题

**端口 8080 被占用**
`start.cmd` 会自动释放 8080 端口再启动。如果手动运行，先释放端口，或在配置中修改 `LISTEN_ADDR`。

**出现 "模型不可用" 错误**
代理会自动重试最多 3 次（指数退避）。若持续报错，说明 Agnes AI 服务暂时过载，稍等片刻再试即可。

**免费额度被限流**
免费额度的速率限制是动态的，等几分钟再试。代理的重试逻辑会自动处理瞬时限流。

**opencode 中看不到 Agnes 提供商**
确保在启动 opencode 之前代理已完全运行。若仍未出现，检查 `~/.config/opencode/opencode.json` 中是否有 `agnes` 提供商条目。

**登录后仪表板仍显示"无计划"**
通过 **仪表板 → 平台登录** 使用你的 agnes-ai.com 账号凭证登录。计划状态每 30 秒自动刷新一次。

---

## 系统要求

- **Node.js** v14+ 或 **Bun** 1.0+
- 无需 `npm install` — 零外部依赖
- 8080 端口可用（可配置）
- Agnes AI API 密钥（[免费注册](https://agnes-ai.com)）

---

## 开源协议

MIT — 基于原版 Agnes 代理，新增了计划状态显示、免费用户图像生成支持、多密钥凭证管理、平台会话持久化及稳定性修复等改进。
