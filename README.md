# MAE: Master Automation Engine

MAE is a collection of Python and shell scripts for decomposing goals, calling configured LLM providers, routing selected tasks through local helper commands, and saving run output. This repository is an integration layer: it depends on tools and configuration under the user's home directory and does not include TCC, Hermes, Paperclip, workflow-dag, llm-burst, provider credentials, or Claude Code itself.

## Repository map

| File | Purpose |
|---|---|
| `mae` | Main CLI for run, plan, sprint, daily, status, deploy, and register actions |
| `mae-paperclip-bridge` | Reads and updates a local Paperclip service, creates local agent files, invokes MAE/TCC, and syncs run logs |
| `mae-session-init` | Session-start hook candidate; reads queue state and prints status |
| `mae-stop-sync` | Stop-hook candidate; marks some stale tasks failed and writes a queue summary |
| `mae-task-intercept` | Prompt-submit hook candidate; injects routing instructions and may start background helpers or add tasks to TCC |
| `README.md` | Repository guide |

## CLI behavior

The executable files document these command forms. They have not been run as part of this review.

```bash
./mae status
./mae run "describe a goal" --plan-only
./mae run "describe a goal"
./mae plan "describe a goal"
./mae sprint
./mae daily
./mae deploy WORKFLOW_NAME
./mae register RESOURCE_FILE
./mae-paperclip-bridge status
./mae-paperclip-bridge run
./mae-paperclip-bridge hire "role"
./mae-paperclip-bridge sync
```

Command names do not imply a dry run:

- `mae run ... --plan-only` still sends the goal to Groq for decomposition; it skips task execution.
- `mae plan ...` sends the goal to Groq and adds the resulting tasks to TCC. It changes the queue.
- `mae sprint` fires all pending TCC tasks.
- `mae daily` adds six preset tasks, fires the TCC queue, and posts a status item to a local Paperclip endpoint.
- `mae deploy` invokes the configured workflow-dag executable for a workflow file.
- `mae register` checks that a file exists and prints the registry path; it does not update the registry.
- The Paperclip bridge's `run`, `hire`, and `sync` modes can execute queued work, write agent files, or post data to the local Paperclip API.

## Providers, data, and local dependencies

The main runner reads `~/.claude/tier0.env` when present and can send task text to Groq, Gemini, DeepSeek, Moonshot, llm-burst, and local Ollama/Hermes tools. Fallbacks may route a prompt to a different provider. The exact route depends on configured keys, local binaries, service availability, and source logic. No credentials should be committed to this repository.

Runs create `~/.claude/tcc-logs` and write task reports there. The runner also writes shared Hermes context under `~/.claude/mae-hermes-ctx.json` and attempts to POST run metadata to Paperclip at `127.0.0.1:3100`. The bridge reads task and log data and can post goals, activities, and generated agent records to that service.

Several scripts assume macOS paths and commands such as `vm_stat`, `top -l`, and home-directory executables. The task-intercept hook reads submitted prompt text; depending on length, keywords, and RAM checks, it can asynchronously send part of that prompt to TCC or launch other local helpers. Review hook code and provider configuration before enabling it.

## Validation and limitations

- The README's former Python package, environment-file, and `setup.py` installation instructions referenced files absent from the repository and have been removed.
- No dependency manifest, automated test suite, or license file is present. GitHub metadata reports no declared license.
- Provider calls, local TCC/Hermes/MAE integrations, Paperclip API behavior, and market/service outcomes are not verified here.
- Some inline descriptions overstate behavior. For example, `mae register` does not register resources, and plan mode is not queue-free.
- No script, hook, provider call, task queue, workflow, or service was run during this documentation review.

## Maintainer

[hmzainjamil](https://github.com/hmzainjamil)
