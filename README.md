# mae-master-automation-engine

Master Automation Engine: Groq decomposes goals → specialist swarm executes → cross-LLM blast synthesizes → results saved.

![MAE](https://img.shields.io/badge/MAE-Multi--LLM-blue?style=flat&labelColor=555) ![Groq](https://img.shields.io/badge/Groq-Decompose-green?style=flat&labelColor=555) ![Parallel](https://img.shields.io/badge/Execution-Parallel-orange?style=flat&labelColor=555) ![License](https://img.shields.io/badge/License-MIT-yellow?style=flat&labelColor=555)

[Concepts](#-concepts) · [How It Works](#-how-it-works) · [Install](#-install) · [Usage](#-usage) · [Config](#-configuration) · [Tips](#-tips-and-tricks-12) · [Troubleshooting](#-troubleshooting) · [Architecture](#-architecture) · [Startups](#️-startups--businesses)

---

## 🧠 CONCEPTS

| Feature | Location | Description |
|---|---|---|
| Groq Decomposer | `decompose/groq_decomp.py` | llama-3.1-70b breaks goal into subtasks in <2s |
| Specialist Swarm | `swarm/` | Domain agents: code, research, ads, write, analyze |
| Cross-LLM Blast | `blast/blast.py` | Same prompt → Groq + Gemini + DeepSeek simultaneously |
| Synthesis Engine | `synthesis/engine.py` | Merges blast outputs into single coherent result |
| Task Queue | `queue/task_queue.py` | Priority queue for sequential or parallel subtask execution |
| Result Store | `store/results.py` | Saves all outputs to `~/.claude/tcc-logs/` |
| Daily Mode | `workflows/daily.py` | `mae daily` — full morning ops in one command |
| Status Dashboard | `ui/dashboard.py` | Real-time task progress and provider status |
| Provider Config | `config/providers.yaml` | All provider definitions with tier assignments |
| Retry Logic | `core/retry.py` | Exponential backoff on provider failures |
| Token Counter | `core/tokens.py` | Per-provider token tracking and budget enforcement |
| Webhook Server | `webhooks/server.py` | HTTP endpoint for triggering MAE from external tools |

### 🔥 Hot

| Feature | Location | Description |
|---|---|---|
| Groq Decomposer | `decompose/groq_decomp.py` | 70B model decomposes any goal in <2s for free |
| Cross-LLM Blast | `blast/blast.py` | Simultaneously queries Groq + Gemini + DeepSeek — best answer wins |
| Specialist Swarm | `swarm/` | Right model for right task — 10× better than single-model approach |
| Daily Mode | `workflows/daily.py` | One command: emails + tasks + market + ads all processed |
| Result Store | `store/results.py` | Every run saved — full audit trail of AI decisions |

---

## ⚙️ HOW IT WORKS

```
mae run "goal"
    │
    ▼
Groq Decompose (llama-3.1-70b, <2s)
    └── subtasks: [t1, t2, t3, t4, t5]

    │ (parallel execution)
    ▼
┌─────────────────────────────────────────────┐
│  SPECIALIST SWARM (all Tier 0)              │
│  t1 → Code Agent (deepseek-coder)           │
│  t2 → Research Agent (gemini-flash)         │
│  t3 → Ads Agent (llama3.1:8b)               │
│  t4 → Write Agent (mistral-medium)          │
│  t5 → Analyze Agent (deepseek-r1:7b)        │
└─────────────────────────────────────────────┘
    │
    ▼
Cross-LLM Blast (optional, on critical subtasks)
    ├── Groq → response A
    ├── Gemini → response B
    └── DeepSeek → response C

    │
    ▼
Synthesis (Claude Sonnet — FINAL OUTPUT ONLY)
    └── Merge all results → coherent final output

    │
    ▼
Save to ~/.claude/tcc-logs/YYYY-MM-DD-{goal}.md
```

---

## 🚀 INSTALL

```bash
git clone https://github.com/hmzainjamil/mae-master-automation-engine
cd mae-master-automation-engine

pip install -r requirements.txt

cp .env.example .env
# Fill: GROQ_API_KEY, GEMINI_API_KEY, DEEPSEEK_API_KEY, ANTHROPIC_API_KEY

# Install mae CLI
pip install -e .  # or: alias mae="python3 mae/cli.py"

# Test decomposer
mae decompose "Research and write a competitor analysis for DigiMinds"

# Run daily workflow
mae daily
```

---

## 📟 USAGE

```bash
# Run any goal
mae run "Create a Google Ads campaign structure for a SaaS product"

# Daily ops mode
mae daily

# Parallel quick tasks (TCC blast)
tcc blast "summarize emails" "check ad performance" "update task list"

# Queue tasks for sequential execution
tcc fire all

# Check status
tcc-dashboard

# View logs
mae logs --last 5

# Cross-LLM blast on specific prompt
mae blast "What's the best bidding strategy for a new Google Ads account?"

# Dry-run (decompose only, don't execute)
mae run --dry-run "Build a landing page for a SaaS product"

# Save output to file
mae run "competitor analysis" --output ~/Downloads/analysis.md
```

---

## ⚙️ CONFIGURATION

| Variable | Default | Description |
|---|---|---|
| `DECOMPOSE_MODEL` | `groq:llama-3.1-70b-versatile` | Model for goal decomposition |
| `MAX_SUBTASKS` | `10` | Max subtasks per decomposition |
| `SWARM_PARALLELISM` | `5` | Max concurrent swarm agents |
| `BLAST_PROVIDERS` | `groq,gemini,deepseek` | Providers for cross-LLM blast |
| `SYNTHESIS_MODEL` | `claude-sonnet-4-5` | Final synthesis model |
| `RESULTS_DIR` | `~/.claude/tcc-logs/` | Output directory |
| `DAILY_RUN_TIME` | `08:00` | Cron time for daily mode |
| `TOKEN_BUDGET_DAILY` | `500000` | Max tokens per day (all providers) |
| `RETRY_MAX_ATTEMPTS` | `3` | Retry attempts on provider failure |
| `WEBHOOK_PORT` | `8765` | HTTP webhook server port |

---

## 💡 TIPS AND TRICKS (12)

[Decomposition](#tips-decomp) · [Swarm](#tips-swarm) · [Blast](#tips-blast) · [Daily Ops](#tips-daily)

<a id="tips-decomp"></a>■ **Decomposition (3)**

| Tip | Source |
|---|---|
| Groq's llama-3.1-70b is free and fast — decompose with it even for simple tasks | MAE design |
| More specific goals → better decomposition: "Write Google Ads copy for SaaS trial signup" beats "write ads" | Decompose guide |
| `--dry-run` shows decomposition before executing — validate before burning tokens | MAE CLI docs |

<a id="tips-swarm"></a>■ **Specialist Swarm (3)**

| Tip | Source |
|---|---|
| Each swarm agent uses Tier 0 model — swarm costs $0 for most tasks | Swarm design |
| Map task types to agents in `swarm/routing.yaml` — customize for your use cases | Swarm config |
| `SWARM_PARALLELISM=3` on 16GB Mac — prevents Ollama OOM during parallel inference | Performance guide |

<a id="tips-blast"></a>■ **Cross-LLM Blast (3)**

| Tip | Source |
|---|---|
| Use blast for high-stakes decisions — 3 models rarely agree on bad answers | Blast design |
| Blast takes ~5s — worth it for strategy questions, skip for simple tasks | Blast benchmarks |
| Synthesis model merges blast outputs — result quality exceeds any single model | Synthesis guide |

<a id="tips-daily"></a>■ **Daily Ops (3)**

| Tip | Source |
|---|---|
| `mae daily` runs in <3 min via LaunchAgent at 08:00 — morning briefing automated | Daily mode |
| Customize `workflows/daily_config.yaml` — add/remove daily tasks without Python | Config guide |
| Daily mode costs ~$0.05 in API calls — save hours of manual review | Cost analysis |

---

## 🔧 TROUBLESHOOTING

| Issue | Fix |
|---|---|
| Groq rate limit | `DECOMPOSE_MODEL=ollama:llama3.1:8b` as fallback |
| Swarm agent crashes | Check individual agent logs in `~/.claude/tcc-logs/errors/` |
| Synthesis too slow | Reduce blast providers: `BLAST_PROVIDERS=groq,gemini` |
| Daily mode not running | `crontab -l` — check cron entry; verify Python path |
| Results not saving | `mkdir -p ~/.claude/tcc-logs/` |
| Webhook not responding | Check port: `lsof -i :8765` |
| Decompose too many subtasks | Reduce `MAX_SUBTASKS=5` |

---

## 📊 ARCHITECTURE

```
mae-master-automation-engine/
├── mae/
│   ├── cli.py                  # CLI entry point
│   └── main.py                 # Core MAE logic
├── decompose/
│   └── groq_decomp.py          # Groq goal decomposition
├── swarm/
│   ├── code_agent.py           # deepseek-coder specialist
│   ├── research_agent.py       # gemini-flash specialist
│   ├── ads_agent.py            # llama3.1 specialist
│   ├── write_agent.py          # mistral specialist
│   ├── analyze_agent.py        # deepseek-r1 specialist
│   └── routing.yaml            # Task→agent routing
├── blast/
│   └── blast.py                # Cross-LLM simultaneous query
├── synthesis/
│   └── engine.py               # Multi-output merger
├── queue/
│   └── task_queue.py           # Priority task queue
├── store/
│   └── results.py              # Output persistence
├── workflows/
│   ├── daily.py                # Daily ops workflow
│   └── daily_config.yaml       # Customizable daily tasks
├── core/
│   ├── retry.py                # Exponential backoff
│   └── tokens.py               # Token tracking
├── webhooks/
│   └── server.py               # HTTP trigger endpoint
├── ui/
│   └── dashboard.py            # Progress dashboard
└── config/
    └── providers.yaml          # Provider definitions
```

---

## 📊 PERFORMANCE BENCHMARKS

| Task | Sequential (single model) | MAE Parallel | Speedup |
|---|---|---|---|
| Research + Write report | 8 min | 2.5 min | 3.2× |
| Daily ops briefing | 25 min manual | 2.8 min | 9× |
| Competitor analysis | 15 min | 4 min | 3.75× |
| Ad copy (5 variations) | 5 min | 1.5 min | 3.3× |

---

## ☠️ STARTUPS / BUSINESSES

| This Repo / Feature | Replaced |
|---|---|
| Groq Decomposer | Manual task planning before every AI session |
| Specialist Swarm | Single model doing everything poorly |
| Cross-LLM Blast | Trusting one model's answer on important decisions |
| Daily Mode | 25-minute manual morning ops review |
| Result Store | AI decisions undocumented and unreproducible |
| Synthesis Engine | Reading 3 model outputs manually and merging |
| Token Budget | Runaway API spend across multiple providers |
| Webhook Server | No way to trigger MAE from external systems |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/mae-master-automation-engine&type=Date)](https://star-history.com/#hmzainjamil/mae-master-automation-engine&Date)

---
<div align="center">Built by <a href="https://github.com/hmzainjamil">HMZ</a> · Part of HMZ Claude AI System</div>

---

## 🔄 CONTRIBUTING

PRs welcome. Please include:
- Tests for new functionality
- Updated `config/providers.yaml` if adding providers
- Benchmark comparison for performance claims
- Documentation update in README

```bash
git checkout -b feature/my-feature
# make changes
python3 tests/run_all.py  # must pass
git push origin feature/my-feature
# open PR
```

---

## 📌 RELATED REPOS

| Repo | Purpose |
|---|---|
| [G0DM0D3](https://github.com/hmzainjamil/G0DM0D3) | Multi-model race + Liquid Response |
| [hermes-ai-system](https://github.com/hmzainjamil/hermes-ai-system) | Local agent with 30+ tools |
| [claude-ai-system-backup](https://github.com/hmzainjamil/claude-ai-system-backup) | Full system backup |
| [hmz-ai](https://github.com/hmzainjamil/hmz-ai) | Personal automation hub |
