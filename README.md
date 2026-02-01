# openclaw-config

Battle-tested configs, scripts, and workspace templates for **OpenClaw / Moltbot (Clawdbot)**.

- Upstream: https://github.com/openclaw/openclaw
- This repo is **not** an SDK or plugin — it’s a set of *copyable* building blocks.

## What you get

- **Memory Engine** (`scripts/memory-engine/`): capture → recall → decay → learn (nightly) + self-review (MISS/FIX) from real logs
- **Gateway config snippets** (`config/*.json5`): compaction, pruning, model fallbacks, Discord setup, memory search
- **Workspace templates** (`templates/`): `AGENTS.md`, `SOUL.md`, `HEARTBEAT.md`, etc.
- **Optional content pipeline** (`content-pipeline/`): cron-driven draft → approval → post → metrics loop

## Who this is for

You’ll like this repo if you:

- run OpenClaw daily and want your setup to be **more stable under heavy tool usage**
- are tired of “self-improvement” prompts that don’t stick
- want a practical starting point for a *real* workspace (templates + scripts), not a blank folder

## Safety / privacy (read before copying)

These files are meant to be copied into your workspace, which means you should treat them like production config:

- **Never commit secrets** (API keys, tokens, cookie DBs)
- Skim any file before you copy it; some templates contain placeholder personal details
- If you fork this repo: keep it clean (no private data, no logs)

## Quickstart (recommended)

Pick only what you want. Most people start with **templates + memory engine**.

```bash
# 1) Clone
git clone https://github.com/unisone/openclaw-config.git
cd openclaw-config

# 2) Copy templates into your workspace (edit them after)
# Replace ~/clawd with your OpenClaw workspace path.
rsync -av templates/ ~/clawd/

# 3) Copy the memory engine
mkdir -p ~/clawd/scripts/
rsync -av scripts/memory-engine/ ~/clawd/scripts/memory-engine/
chmod +x ~/clawd/scripts/memory-engine/*.sh

# 4) Run an initial capture (creates/updates memory/store.json)
cd ~/clawd
bash scripts/memory-engine/capture.sh

# 5) Try recall
bash scripts/memory-engine/recall.sh "context overflow" 
```

### Add the nightly job

Run nightly learning (capture → decay → self-review → insights):

- Option A: add a cron job in OpenClaw that runs `bash scripts/memory-engine/learn.sh`
- Option B: run it manually at first to confirm it matches your workflow

## Gateway config snippets

Config examples live in `config/*.json5`.

Typical workflow:

1. Open your OpenClaw config file (`~/.openclaw/openclaw.json` on most installs)
2. Copy the snippet you need
3. Merge it into your config (don’t blindly replace)

Start here:

- `config/compaction-and-pruning.json5` — highest impact stability settings
- `config/context-overflow-prevention.json5` — defensive settings for heavy tool use
- `config/model-aliases-and-fallbacks.json5` — provider fallbacks
- `config/memory-search.json5` — semantic search over memory files
- `config/discord-setup.json5` — practical Discord defaults

## Repo layout

```text
openclaw-config/
  agents/               # Multi-agent persona prompts
  config/               # JSON5 snippets you can merge into your gateway config
  content-pipeline/     # Optional: cron-driven content workflow
  docs/                 # Deep dives (e.g., context overflow prevention)
  scripts/memory-engine/# Capture/recall/decay/learn + self-review
  templates/            # Workspace bootstrap files
  workflows/            # Optional Lobster workflows
```

## Contributing

PRs welcome, but keep it strict:

- **Must be tested** in a real OpenClaw workspace
- **No personal data** (keys, usernames, emails, logs)
- Explain *why* a setting/script exists, not just what it does

See [CONTRIBUTING.md](./CONTRIBUTING.md).

## License

MIT (see [LICENSE](./LICENSE))
