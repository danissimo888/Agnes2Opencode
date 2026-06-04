# Agnes2Opencode

**OpenAI-compatible proxy server for [Agnes AI](https://agnes-ai.com)** — use Agnes models with any OpenAI client, get a beautiful dashboard, and auto-configure opencode. Zero npm dependencies.

[![Node.js](https://img.shields.io/badge/Node.js-v14%2B-green)](https://nodejs.org) [![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE) [![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero-brightgreen)]()

🇨🇳 [中文版 README](README_CN.md)

---

<img width="1206" height="683" alt="Dashboard UI" src="https://github.com/user-attachments/assets/96f3e2d1-c566-4926-9fbb-e173f6c8652b" />

<img width="638" height="159" alt="opencode integration" src="https://github.com/user-attachments/assets/1a963114-d3c9-4e3c-9ac6-9516957b793e" />

<img width="1301" height="866" alt="Plan status and key management" src="https://github.com/user-attachments/assets/c2dd6e32-9f06-464c-ae68-c7d7f87c91c7" />

---

## What Is This?

Agnes2Opencode is a local proxy server that sits between your AI tools and the Agnes AI API. It exposes a standard OpenAI-compatible endpoint (`/v1/chat/completions`) so you can connect **any OpenAI client** — including opencode, Cursor, Continue, or your own code — to Agnes AI models.

**Who is this for?**
- opencode users who want to use Agnes AI as a provider
- Developers building apps with the OpenAI SDK who want free Agnes AI access
- Anyone who wants a dashboard to manage their Agnes AI keys and usage

---

## Features

- **OpenAI-Compatible API** — Standard `/v1/chat/completions` and `/v1/models` endpoints
- **Streaming Support** — Server-Sent Events streaming for real-time responses
- **Auto-Config opencode** — Automatically sets up Agnes as an opencode provider on startup
- **Beautiful Dashboard** — Liquid-glass UI with model toggles, key manager, plan status, cache stats
- **Multi-Key Rotation** — Distribute load across multiple API keys with sticky session tracking
- **Platform Login** — Log in with your Agnes AI account, view plan status and usage
- **Response Caching** — LRU cache for non-streaming responses (configurable TTL and size)
- **Retry Logic** — Automatic retry with exponential backoff for transient upstream errors
- **AI Wallpaper** — Generate AI backgrounds or use Bing daily photos in the dashboard
- **Tool Schema Normalization** — Fixes `$ref`/`$defs` in tool schemas before forwarding to upstream
- **Model Remapping** — Translates legacy model IDs to current equivalents automatically
- **Test Mode** — Return mock responses without consuming API credits
- **Zero Dependencies** — No `npm install` needed — pure Node.js built-in modules only

---

## Tutorial: Getting Started

> **Quick start:** Get Agnes AI key → copy `config.example.json` to `config.json` → paste key → `node proxy.js` → open http://localhost:8080

Follow these steps from zero to a working setup.

### Step 1 — Get an Agnes AI API Key

1. Go to [agnes-ai.com](https://platform.agnes-ai.com/) and create a free account
2. Navigate to your API keys page
3. Copy your key — it starts with `sk-`

> **Free Tier:** Agnes AI 2.0 models (like `agnes-2.0-flash`) are free with no credit card required. Rate limits are dynamic based on server load rather than a fixed monthly quota.

---

### Step 2 — Download the Proxy

**Option A: Clone with Git**
```bash
git clone https://github.com/danissimo888/Agnes2Opencode.git
cd Agnes2Opencode
```

**Option B: Download ZIP**

Click **Code → Download ZIP** on the GitHub page, then extract it.

---

### Step 3 — Add Your API Key

Copy the example config and add your key:

```bash
# macOS / Linux
cp .config/config.example.json .config/config.json

# Windows Command Prompt
copy .config\config.example.json .config\config.json
```

Then open `.config/config.json` and replace the placeholder:

```json
{
  "API_KEY": "sk-your-agnes-api-key-here"
}
```

> `config.json` is listed in `.gitignore` — it will never be accidentally committed.

**Alternative — environment variable (no file editing needed):**

```bash
# Windows Command Prompt
set AGNES_API_KEY=sk-your-key
node proxy.js

# Windows PowerShell
$env:AGNES_API_KEY="sk-your-key"
node proxy.js

# macOS / Linux
AGNES_API_KEY=sk-your-key node proxy.js
```

---

### Step 4 — Start the Proxy

**Windows (easiest):**
Double-click `start.cmd` — it auto-detects Bun or Node.js, frees port 8080 if busy, and starts the proxy.

**Any OS:**
```bash
node proxy.js
```

**With Bun (faster startup):**
```bash
bun proxy.js
```

You should see output like:
```
[Agnes Proxy] Listening on http://127.0.0.1:8080
[Agnes Proxy] Dashboard: http://localhost:8080
```

---

### Step 5 — Open the Dashboard

Visit **http://localhost:8080** in your browser.

The dashboard lets you:
- View your plan status and usage
- Add, edit, or delete API keys
- Toggle which models are available
- Monitor cache hits and API key health
- Log in to your Agnes AI platform account
- Switch wallpaper mode (None / Bing / AI Image)

---

### Step 6 — Connect to opencode

1. Make sure the proxy is running (Step 4)
2. Restart opencode
3. When prompted for a provider, select **agnes**

The proxy automatically writes the Agnes provider config to `~/.config/opencode/opencode.json`. A backup is created before the first edit. No manual JSON editing needed.

---

### Step 7 — Use with Any OpenAI Client

Point your OpenAI client at `http://localhost:8080/v1`:

**JavaScript / Node.js:**
```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  baseURL: 'http://localhost:8080/v1',
  apiKey: 'not-needed'  // proxy handles auth
});

const response = await client.chat.completions.create({
  model: 'agnes-2.0-flash',
  messages: [{ role: 'user', content: 'Hello!' }]
});

console.log(response.choices[0].message.content);
```

**Python:**
```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8080/v1",
    api_key="not-needed"
)

response = client.chat.completions.create(
    model="agnes-2.0-flash",
    messages=[{"role": "user", "content": "Hello!"}]
)

print(response.choices[0].message.content)
```

**cURL:**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "agnes-2.0-flash",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

---

## Available Models

| Model ID | Type | Free Tier | Context |
|----------|------|-----------|---------|
| `agnes-2.0-flash` | Text | ✅ Yes | 256K |
| `agnes-1.5-flash` | Text | ✅ Yes | 256K |
| `agnes-image-2.0-flash` | Image | ✅ Yes | — |
| `agnes-image-2.1-flash` | Image | ✅ Yes | — |
| `agnes-video-v2.0` | Video | ✅ Yes | — |

Models are fetched dynamically from Agnes AI on startup (5-minute cache). Legacy model IDs like `sapiens-ai/agnes-1.5-pro` are automatically remapped to current equivalents.

---

## How the Free Tier Works

- **No credit card required** — The baseline models are free indefinitely
- **Dynamic rate limits** — Instead of a hard monthly quota, the platform throttles based on real-time server load. Speeds may slow during peak hours to prioritize paid traffic
- **Retry built-in** — The proxy automatically retries on transient errors with exponential backoff (5s → 10s → 15s, up to 3 attempts)

If you find the service useful, consider [subscribing](https://platform.agnes-ai.com/subscribe/subscription?from=website) to support the platform.

---

## Configuration Reference

Edit `.config/config.json` or set the equivalent environment variable:

| Key | Env Variable | Description | Default |
|-----|-------------|-------------|---------|
| `LISTEN_ADDR` | `LISTEN_ADDR` | Proxy listen address | `127.0.0.1:8080` |
| `UPSTREAM_BASE_URL` | `UPSTREAM_BASE_URL` | Agnes API base URL | `https://apihub.agnes-ai.com` |
| `API_KEY` | `AGNES_API_KEY` | Your Agnes AI API key | — |
| `REQUEST_TIMEOUT` | `REQUEST_TIMEOUT` | Upstream request timeout | `15m` |
| `API_KEYS` | `API_KEYS` | Proxy auth keys (empty = open access) | `[]` |
| `ENABLED_MODELS` | — | Models visible to clients | all |
| `CACHE_TTL` | `CACHE_TTL` | Response cache time-to-live | `60s` |
| `CACHE_MAX_SIZE` | `CACHE_MAX_SIZE` | Max cached responses | `100` |
| `CACHE_ENABLED` | `CACHE_ENABLED` | Enable response caching | `true` |
| `WALLPAPER_MODE` | — | `none`, `bing`, or `ai` | `bing` |
| `WALLPAPER_PROMPT` | — | Prompt for AI wallpaper | `realistic vibrant colorful mountain range landscape` |
| `TEST_MODE` | `TEST_MODE` | Return mock responses | `false` |

---

## Multi-Key Setup

Add multiple API keys to distribute requests across accounts:

```json
{
  "TOKENS": [
    { "name": "Key 1", "token": "sk-your-first-key" },
    { "name": "Key 2", "token": "sk-your-second-key" }
  ]
}
```

Each key can also store platform credentials for auto-login:

```json
{
  "TOKENS": [
    {
      "name": "Work",
      "token": "sk-your-key",
      "platformUsername": "you@example.com",
      "platformPassword": "your-password"
    }
  ]
}
```

**How rotation works:** Each conversation is identified by an MD5 fingerprint of the first user message. Follow-up requests in the same conversation are pinned to the same key. New conversations cycle through keys round-robin. Messages are stamped with `[KeyName|sessN]` for server-side traceability.

You can also manage keys from the **Dashboard → Manage Keys** panel without editing JSON.

---

## Restricting Access (Proxy API Keys)

By default the proxy is open — anyone on the local network can use it. To require authentication:

```json
{
  "API_KEYS": ["my-secret-key-1", "my-secret-key-2"]
}
```

Clients must send the key in the header:

```bash
curl -H "Authorization: Bearer my-secret-key-1" http://localhost:8080/v1/models
# or
curl -H "x-api-key: my-secret-key-1" http://localhost:8080/v1/models
```

---

## API Endpoints

### Core

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/healthz` | Health check — API key status, uptime, cache stats |
| `GET` | `/v1/models` | OpenAI-format model list |
| `POST` | `/v1/chat/completions` | Chat completions (streaming, caching, retry) |

### Management

| Method | Path | Description |
|--------|------|-------------|
| `GET/POST` | `/api/config` | Read / write proxy config |
| `GET` | `/api/validate` | Validate API key against Agnes upstream |
| `GET` | `/api/models` | Model list with metadata |
| `GET` | `/api/bg` | Dashboard wallpaper image |
| `POST` | `/api/generate-image` | Generate AI wallpaper |
| `GET/POST` | `/api/keys` | Multi-key CRUD |
| `GET` | `/api/account` | Platform account info |
| `GET` | `/api/step-plan-status` | Subscription plan + usage windows |
| `POST` | `/api/login` | Login with platform credentials |
| `POST` | `/api/logout` | Clear platform session |
| `GET/DELETE` | `/api/cache` | View / clear response cache |

---

## Troubleshooting

**Port 8080 is already in use**
`start.cmd` automatically kills whatever is using port 8080 before starting. If running manually, free the port first or change `LISTEN_ADDR` in config.

**"Model unavailable" errors**
The proxy retries automatically up to 3 times with exponential backoff. If errors persist, the Agnes AI service may be temporarily overloaded — wait a moment and try again.

**Rate limited on free tier**
Free tier rate limits are dynamic. Wait a few minutes and retry. The proxy's retry logic handles transient throttling automatically.

**opencode doesn't show Agnes provider**
Make sure the proxy is fully started before launching opencode. The auto-config writes on startup. If it still doesn't appear, check `~/.config/opencode/opencode.json` for the `agnes` provider entry.

**Dashboard shows "No Plan" even after login**
Log in through **Dashboard → Platform Login** with your agnes-ai.com account credentials. The plan status refreshes every 30 seconds.

---

## Architecture Overview

```
proxy.js
├── Config System         — JSON + env vars, per-token credentials, duration parsing
├── LRU Response Cache    — MD5-keyed, configurable TTL/max size, streaming excluded
├── UpstreamClient        — HTTP client for apihub.agnes-ai.com
├── Platform Login        — Session management, JWT persistence, auto-login on restart
├── Model Registry        — Dynamic fetch (5min TTL), fallback list, legacy remapping
├── Tool Schema Norm.     — $ref resolution, nullable simplification
├── Retry Logic           — 3 attempts, exponential backoff (5s/10s/15s)
├── Session Tracking      — MD5 fingerprint → key index, sticky sessions, stamping
├── Opencode Config       — Auto-writes provider config, backup, cleanup
└── HTTP Router           — OpenAI + management endpoints

dashboard.html
├── Liquid Glass Engine   — Canvas displacement maps with refraction effects
├── Plan Status Fieldset  — Subscription, usage bars, login/subscribe CTA
├── Key Manager           — Add/edit/delete keys, platform account display
├── Model Toggle          — Enable/disable models with capability badges
├── Platform Login Modal  — Credentials form, session status
├── Wallpaper Toggle      — None / Bing / AI Image + prompt input
└── Auto-refresh          — Health (15s) + plan status (30s) polling
```

---

## Requirements

- **Node.js** v14+ OR **Bun** 1.0+
- No `npm install` — zero external dependencies
- Port 8080 available (configurable)
- Agnes AI API key ([free signup](https://agnes-ai.com))

---

## License

MIT — Based on the original Agnes proxy, with improvements including plan status display, plan-agnostic image generation, multi-key credential management, platform session persistence, and stability fixes.
