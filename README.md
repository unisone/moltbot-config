<p align="center">
  <img src="docs/assets/openclaw-config-cover.png" alt="openclaw-config" width="600" />
</p>

<h1 align="center">openclaw-config</h1>

<p align="center">
  Production-tested configs, scripts, and workspace templates for <a href="https://github.com/openclaw/openclaw">OpenClaw</a>.<br />
  Not an SDK — just copyable building blocks from a real daily-driver setup.<br />
  <strong>Release v2026.02.16</strong> • Compatible with OpenClaw 2026.2.14+
</p>

<p align="center">
  <a href="#quickstart">Quickstart</a> •
  <a href="#whats-included">What's Included</a> •
  <a href="#gateway-config-snippets">Configs</a> •
  <a href="#skill-routing-overrides">Skills</a> •
  <a href="#workspace-templates">Templates</a> •
  <a href="#contributing">Contributing</a>
</p>

---

## Quickstart

Pick what you need. Most people start with **templates** or **templates + task runner**.

```bash
# Clone
git clone https://github.com/unisone/openclaw-config.git
cd openclaw-config

# Copy templates into your workspace
rsync -av templates/ ~/.openclaw/workspace/

# (Optional) Copy the task runner
rsync -av scripts/taskrunner/ ~/.openclaw/workspace/scripts/taskrunner/

# (Optional) Copy skill routing overrides
rsync -av skills/ ~/.openclaw/workspace/skills/
```

> **Note:** Replace `~/.openclaw/workspace/` with your actual workspace path if different.

---

## What's Included

| Directory | Description |
|-----------|-------------|
| `templates/` | Workspace bootstrap files — `AGENTS.md`, `SOUL.md`, `STATE.md`, `HEARTBEAT.md`, `IDENTITY.md`, `USER.md`, `artifacts/` |
| `skills/` | Workspace-level SKILL.md overrides that reduce misfires in overlapping domains |
| `config/` | Gateway config snippets (JSON5) — compaction, pruning, model fallbacks, memory, search, Slack |
| `scripts/memory-engine/` | Shell-based memory lifecycle: capture → recall → decay → learn + self-review |
| `scripts/taskrunner/` | Python task automation with retry logic, file locking, structured logging |
| `agents/` | Multi-agent persona prompts (researcher, architect, security auditor, etc.) |
| `docs/` | Operational guides — context overflow, agent memory research, skill routing, Slack migration, model fallback strategy |
| `content-pipeline/` | Optional cron-driven content workflow: draft → approve → post → metrics |

---

## Workspace Templates

The `templates/` directory provides a complete workspace scaffold:

- **`AGENTS.md`** — Session boot sequence, memory hierarchy, group chat etiquette, heartbeat system, artifact conventions (~220 lines of battle-tested instructions)
- **`SOUL.md`** — Agent personality and philosophy. Opinionated by design — edit to match your style.
- **`STATE.md`** — Live working context (max 3KB). Survives compaction and session resets. Priority markers: 🔴 ACTIVE / 🟡 WAITING / ⚪ DONE
- **`HEARTBEAT.md`** — Proactive check template: STATE maintenance, cron health, inbox/calendar rotation, self-improvement
- **`IDENTITY.md`** — Agent identity (name, emoji, avatar, history)
- **`USER.md`** — About the human (preferences, projects, context)
- **`artifacts/`** — Structured output directory: `reports/`, `code/`, `data/`, `images/`, `temp/`

---

## Skill Routing Overrides

OpenClaw's bundled skills sometimes collide in overlapping domains. These workspace-level overrides add **"Don't use for..."** negative examples to improve routing accuracy.

| Domain | Skills | Routing Logic |
|--------|--------|---------------|
| Email | `gog`, `himalaya` | gog → Gmail/Google Workspace · himalaya → IMAP/SMTP |
| Notes | `apple-notes`, `bear-notes`, `obsidian`, `notion` | Each gets explicit boundaries against the others |
| Transcription | `openai-whisper`, `openai-whisper-api` | Local CLI vs cloud API |
| Messaging | `imsg` | Fallback iMessage CLI; prefer bluebubbles if available |
| Code | `coding-agent` | Multi-step coding sessions; not for simple shell commands |
| Summarize | `summarize` | URLs/podcasts/files; not for simple HTML extraction |

Based on OpenAI's [Shell + Skills + Compaction](https://openai.com) guidance — Glean reported **20% misfire reduction** after adding negative examples.

> Overrides mask bundled updates. When OpenClaw ships improved descriptions upstream, delete the override to fall back. Tracked in [openclaw/openclaw#14748](https://github.com/openclaw/openclaw/issues/14748).

---

## Gateway Config Snippets

Production-tested config examples in `config/*.json5`. **Merge into your config — don't replace.**

### Essential

| Config | What It Does |
|--------|-------------|
| `compaction-and-pruning.json5` | Highest-impact stability settings (reserve tokens, soft thresholds) |
| `context-overflow-prevention.json5` | Defensive settings for heavy tool use |
| `model-aliases-and-fallbacks.json5` | Cross-provider fallbacks (Opus → Sonnet → GPT) to prevent cascading failures |
| `memory-search.json5` | Semantic search over workspace memory files |
| `slack-setup.json5` | Slack configuration (socket mode, allowlist policies, session sprawl prevention) |

### Power User

| Config | What It Does |
|--------|-------------|
| `pro-optimization.json5` | Maximum capability without sacrificing stability — longer sessions, faster recovery, aggressive caching |
| `feature-unlocks.json5` | Enable production-ready features that ship disabled by default — auto-analysis, link fetch, typing indicator, heartbeats |
| `memory-lancedb-setup.json5` | LanceDB + OpenAI embeddings for long-term memory |
| `web-search-perplexity.json5` | Perplexity search (no rate limits with paid key) |

```bash
# Apply configs, then restart
openclaw gateway restart
```

> **Tip:** Apply `pro-optimization` first, run for a few days, then add `feature-unlocks`. Changing everything at once makes debugging harder.

---

## Documentation

Key operational guides in `docs/`:

| Doc | What It Covers |
|-----|---------------|
| `agent-system-research.md` | Memory systems research (4500+ words) — SQLite+vector, tiered memory, consolidation |
| `session-context-overflow-fix.md` | Context overflow prevention strategies and settings |
| `skill-routing-improvements.md` | Skill routing audit + negative examples to reduce misfires |
| `slack-migration-notes.md` | Slack `dmPolicy` → `dm.policy` migration in OpenClaw 2026.2.14 |
| `model-fallback-strategy.md` | Why cross-provider fallbacks prevent cascading failures |
| `diagnosis-protocol.md` | Systematic troubleshooting for agent issues |
| `content-strategy/` | Content framework, image specs, posting guidelines |

---

## Scripts

### Memory Engine (`scripts/memory-engine/`)

Shell-based memory lifecycle: `capture.sh` → `recall.sh` → `decay.sh` → `learn.sh`

- Nightly learning extracts errors, corrections, and insights from daily logs
- Self-review system (MISS/FIX tags) prevents repeated mistakes
- See `scripts/memory-engine/README.md` for setup

### Task Runner (`scripts/taskrunner/`)

Python-based task automation — replacement for fragile shell scripts:

- Retry logic with exponential backoff
- File locking (no concurrent runs)
- Structured JSON logging (`logs/tasks.jsonl`)
- Alert system for heartbeat integration (`alerts/pending.json`)
- Example tasks: `memory_capture`, `memory_consolidate`, `system_health`

```bash
python3 ~/.openclaw/workspace/scripts/taskrunner/runner.py memory_capture
```

---

## Agents

Drop-in persona prompts for `sessions_spawn` in `agents/`:

- `deep-researcher.md` — Multi-source research with citation
- `architect.md` — System design and technical architecture
- `security-auditor.md` — Security review and hardening
- `content-writer.md` — Content drafting with voice/tone consistency
- `ux-designer.md` — UX review and design recommendations
- `market-researcher.md` — Market analysis and competitive intelligence
- `project-planner.md` — Project breakdown and timeline estimation
- `devils-advocate.md` — Structured counterarguments

---

## Repo Layout

```
openclaw-config/
├── agents/               # Multi-agent persona prompts
├── config/               # Gateway config snippets (JSON5)
├── content-pipeline/     # Optional: cron-driven content workflow
├── docs/
│   ├── agent-system-research.md           # Memory systems research (4500+ words)
│   ├── session-context-overflow-fix.md    # Context overflow prevention
│   ├── skill-routing-improvements.md      # Skill routing audit + improvements
│   └── content-strategy/                  # Content framework + specs
├── scripts/
│   ├── memory-engine/    # Shell-based capture/recall/decay/learn
│   └── taskrunner/       # Python task automation
├── skills/               # Workspace-level SKILL.md routing overrides
├── templates/            # Workspace bootstrap files
│   └── artifacts/        # Structured output directory convention
└── workflows/            # Optional Lobster workflows
```

---

## Safety & Privacy

These files are meant to be copied into your workspace. Treat them like production config:

- **Never commit secrets** — API keys, tokens, cookie databases
- **Review before copying** — some templates contain placeholder values
- **Fork responsibly** — no personal data, no logs

See [SECURITY.md](./SECURITY.md) for reporting vulnerabilities.

---

## Contributing

PRs welcome. Requirements:

- Must be tested in a real OpenClaw workspace
- No personal data (keys, usernames, emails, logs)
- Explain *why* a setting exists, not just what it does

See [CONTRIBUTING.md](./CONTRIBUTING.md).

---

## License

[MIT](./LICENSE)
