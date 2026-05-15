# mae-master-automation-engine
Single command deploys everything — Tier 0 LLM blast + 200 skills + MCP tools + Paperclip CEO layer

![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat&labelColor=555&logo=python)
![Claude](https://img.shields.io/badge/Claude-Code-cc785c?style=flat&labelColor=555)
![Groq](https://img.shields.io/badge/Groq-Decompose-F55036?style=flat&labelColor=555)
![Gemini](https://img.shields.io/badge/Gemini-Flash-4285F4?style=flat&labelColor=555)
![Ollama](https://img.shields.io/badge/Ollama-Local-white?style=flat&labelColor=555)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat&labelColor=555)

[Concepts](#-concepts) · [How It Works](#️-how-it-works) · [Install](#-install) · [Commands](#-commands) · [Tips](#-tips-and-tricks-10) · [Startups](#️-startups--businesses)

---

## 🧠 CONCEPTS

| Feature | Location | Description |
|---------|----------|-------------|
| [**mae**](mae) | `mae` | Master entry point — 828 lines, routes all commands |
| [**mae-paperclip-bridge**](mae-paperclip-bridge) | `mae-paperclip-bridge` | Syncs task state with Paperclip AI at http://127.0.0.1:3100 |
| [**mae-session-init**](mae-session-init) | `mae-session-init` | Initializes all LLMs, MCPs, skills at session start |
| [**mae-stop-sync**](mae-stop-sync) | `mae-stop-sync` | Graceful shutdown — saves state, syncs logs, stops daemons |
| [**mae-task-intercept**](mae-task-intercept) | `mae-task-intercept` | Hooks every Claude prompt — routes to cheapest capable model |
| [**TCC Logs**](~/.claude/tcc-logs/) | `~/.claude/tcc-logs/` | All task outputs saved with timestamp + model used |

### 🔥 Hot

| Feature | Location | Description |
|---------|----------|-------------|
| [**Parallel LLM Blast**](mae) | `mae run "goal"` | Fires Groq+Gemini+DeepSeek+GPT+Ollama simultaneously — fastest wins |
| [**Zero Human Ops**](mae-paperclip-bridge) | `mae daily` | Full daily agency ops: leads, content, audits, KPIs — no input needed |
| [**Task Intercept Hook**](mae-task-intercept) | `mae-task-intercept` | Every Claude prompt checked — simple tasks routed to Tier 0, saves quota |

---

## ⚙️ HOW IT WORKS

```
mae run "achieve this goal"
         ↓
Groq (fastest) decomposes goal → subtasks
         ↓
Specialist swarm fires in parallel:
  ├── Groq       → reasoning tasks
  ├── Gemini Flash → search + summarize
  ├── DeepSeek V3 → code + analysis
  ├── GPT-4o-mini → structured output
  └── Ollama local → simple/private tasks
         ↓
Results synthesized → saved to ~/.claude/tcc-logs/YYYY-MM-DD/
         ↓
Paperclip CEO layer notified via mae-paperclip-bridge
```

**Daily ops pipeline:** `mae daily` triggers leads → content → audit → KPI check → brief — all autonomous.

---

## 🚀 INSTALL

```bash
git clone https://github.com/hmzainjamil/mae-master-automation-engine
cd mae-master-automation-engine
cp mae mae-* ~/.claude/bin/
chmod +x ~/.claude/bin/mae ~/.claude/bin/mae-*
```

**Set API keys in** `~/.claude/tier0.env`:
```bash
GROQ_API_KEY=...
GEMINI_API_KEY=...
DEEPSEEK_API_KEY=...
OPENAI_API_KEY=...
```

---

## 📟 COMMANDS

| Command | Description |
|---------|-------------|
| `mae run "goal"` | Decompose + parallel execute everything |
| `mae plan "goal"` | Show execution plan without firing |
| `mae daily` | Full daily ops automation |
| `mae sprint` | Fire ALL pending TCC tasks right now |
| `mae status` | Show all active operations |
| `mae deploy WORKFLOW` | Deploy specific named workflow |
| `mae stop` | Graceful shutdown via mae-stop-sync |

---

## 💡 TIPS AND TRICKS (10)

[routing](#tips-routing) · [daily](#tips-daily) · [paperclip](#tips-paperclip) · [logs](#tips-logs)

<a id="tips-routing"></a>■ **Routing (3)**

| Tip | Source |
|-----|--------|
| `mae-task-intercept` runs on every Claude prompt via SessionStart hook — zero manual activation | [HMZ](https://github.com/hmzainjamil) |
| Groq decomposes because it's fastest (200+ tok/s) — decomposition happens in <1s | [DigiMinds](https://github.com/hmzainjamil) |
| Set `MAE_MODE=conservative` in tier0.env to only use free/cheap Tier 0 models | [HMZ](https://github.com/hmzainjamil) |

<a id="tips-daily"></a>■ **Daily Ops (3)**

| Tip | Source |
|-----|--------|
| `mae daily` runs via LaunchAgent at 8 AM — check `~/Library/LaunchAgents/` | [HMZ](https://github.com/hmzainjamil) |
| Daily output summary lands in `~/.claude/tcc-logs/daily-YYYY-MM-DD.json` | [HMZ](https://github.com/hmzainjamil) |
| Combine with `tcc fire all` after `mae plan` for max parallelism | [DigiMinds](https://github.com/hmzainjamil) |

<a id="tips-paperclip"></a>■ **Paperclip (2)**

| Tip | Source |
|-----|--------|
| Paperclip runs at http://127.0.0.1:3100 — `mae-paperclip-bridge sync` before daily ops | [HMZ](https://github.com/hmzainjamil) |
| Bridge auto-retries 3x if Paperclip is down — tasks queue locally | [HMZ](https://github.com/hmzainjamil) |

<a id="tips-logs"></a>■ **Logs (2)**

| Tip | Source |
|-----|--------|
| `ls ~/.claude/tcc-logs/ | sort -r | head -5` — see last 5 task runs | [HMZ](https://github.com/hmzainjamil) |
| Each log has `model_used`, `tokens`, `cost_usd`, `duration_ms` — full audit trail | [DigiMinds](https://github.com/hmzainjamil) |

---

## ☠️ STARTUPS / BUSINESSES

| This Repo / Feature | Replaced |
|-|-|
| **mae run (parallel LLM blast)** | [LangChain](https://langchain.com), [AutoGen](https://microsoft.github.io/autogen/), [CrewAI](https://crewai.com) |
| **mae daily (autonomous ops)** | [Zapier](https://zapier.com), [Make.com](https://make.com), [n8n](https://n8n.io) — zero monthly fee |
| **mae-task-intercept** | [LangSmith](https://smith.langchain.com), [Helicone](https://helicone.ai), [PromptLayer](https://promptlayer.com) |
| **mae-paperclip-bridge** | [Salesforce Einstein](https://salesforce.com), [HubSpot AI](https://hubspot.com) — self-hosted |
| **Whole MAE system** | [AgentGPT](https://agentgpt.reworkd.ai), [BabyAGI](https://github.com/yoheinakajima/babyagi), [SuperAGI](https://superagi.com) |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/mae-master-automation-engine&type=Date)](https://star-history.com/#hmzainjamil/mae-master-automation-engine&Date)

---

<div align="center">
Built by <a href="https://github.com/hmzainjamil">HMZ</a> · Part of the <a href="https://github.com/hmzainjamil/claude-ai-system">HMZ Claude AI System</a> · Zero-human agency ops
</div>
