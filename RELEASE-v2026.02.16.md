# Release Notes: v2026.02.16

Release date: 2026-02-16  
Compatible with: OpenClaw 2026.2.14+

## Summary

This release updates the openclaw-config repo with production-tested improvements from live deployment since v2026.02.12. Key themes:
- **Cross-provider fallback strategy** to prevent cascading failures
- **Slack configuration** setup and migration guide
- **Memory flush improvements** with STATE.md-first workflow
- **Enhanced system health checking** with rate-limit awareness
- **Updated workspace templates** with improved group chat guidelines

## Config Updates

### 1. model-aliases-and-fallbacks.json5
- ✅ Added `claude-opus-4-6` with alias `"opus46"`
- ✅ Added comment documenting alias collision lesson learned (opus → two different models)
- ✅ Kept kimi as cheap subagent model with cross-provider fallbacks

**Rationale:** Single-provider fallback chains (Codex-only) caused 4+ hour outages during OpenAI rate-limiting incidents. Cross-provider strategy ensures at least one model remains available.

### 2. compaction-and-pruning.json5
- ✅ Added production-tested `memoryFlush.prompt` and `memoryFlush.systemPrompt`
  - Emphasizes STATE.md-first workflow (survives compaction)
  - 3KB limit enforcement
- ✅ Updated `contextPruning.keepLastAssistants: 5` (was 3)
- ✅ Kept `softThresholdTokens: 120000` (repo value better than live 8000)
- ✅ Kept `minPrunableToolChars: 50000` (repo value better than live 2000)

**Rationale:** Production testing showed STATE.md updates during memoryFlush are critical for session continuity after compaction. Higher keepLastAssistants preserves more context for multi-turn conversations.

### 3. feature-unlocks.json5
- ✅ Added `memorySearch.experimental.sessionMemory: true`
- ✅ Added hybrid search config (vectorWeight 0.7, textWeight 0.3, maxResults 6)
- ✅ Updated heartbeat prompt (Slack-specific, removed Discord refs)
- ✅ Added `heartbeat.every: "1h"`
- ✅ Updated `media.audio.maxBytes: 52428800` (50MB, was 25MB)
- ✅ Updated `links.maxLinks: 5` (was 3)
- ✅ Added `tools.elevated` section example with security warning
- ✅ Added `tools.exec` section example

**Rationale:** Production values from daily use. 50MB audio allows full voice messages. 5 links supports multi-source research. Hybrid search improves memory recall accuracy.

### 4. context-overflow-prevention.json5
- ✅ Added `subagents.thinking: "high"`
- ✅ Confirmed `subagents.archiveAfterMinutes: 60`

**Rationale:** Subagents handle complex work (research, drafting, analysis) — they need thinking budget. 60-minute archive prevents session sprawl while allowing completion of long-running tasks.

### 5. NEW: slack-setup.json5
- ✅ Created comprehensive Slack configuration example
- ✅ Documented socket mode setup
- ✅ Explained `groupPolicy: "allowlist"` (prevents session sprawl)
- ✅ Documented `dm.policy` migration from `dmPolicy` (2026.2.14 breaking change)
- ✅ Included channel/user ID discovery instructions
- ✅ Configured reaction notifications: "off" (prevents spam)

**Rationale:** Slack is a major deployment target. Default settings cause hundreds of idle sessions. Allowlist + reaction config required for production use.

### 6. pro-optimization.json5
- ✅ Reviewed — no changes needed (already optimal)

## Template Updates

### templates/AGENTS.md
- ✅ Copied from live workspace with personal data scrubbed
- ✅ Removed specific Slack channel IDs → generic placeholders
- ✅ Removed specific user IDs
- ✅ Removed "Alex" and personal references → generic "your human"
- ✅ Removed Meridian-specific references
- ✅ Kept structural improvements:
  - STATE.md emphasis (loaded every session, max 3KB)
  - Memory DB hygiene (check before store, delete duplicates)
  - Heartbeat check guidelines
  - Group chat participation rules (know when to speak/stay silent)
  - Reaction usage guidelines (lightweight social signals)

### templates/SOUL.md
- ✅ Copied from live workspace
- ✅ Scrubbed personal references
- ✅ Kept new sections:
  - **Growth** (autonomous self-improvement mandate)
  - **Agentic Mode** (action over discussion, full loops, sub-agent orchestration)

### templates/HEARTBEAT.md
- ✅ Copied from live workspace with generalization
- ✅ Replaced specific channel IDs → placeholders like "#ops"
- ✅ Replaced specific scripts → generic task runner references
- ✅ Kept self-improvement rotation section

### templates/STATE.md
- ✅ Kept existing repo version (it's a starter template, not meant to match live)

## Scripts Updates

### scripts/taskrunner/tasks/system_health.py
- ✅ Copied improved version from live workspace
- ✅ Enhanced cron job health checking:
  - Rate-limit-aware (distinguishes rate limits from actual failures)
  - Handles both dict and list cron formats
  - Categorizes failures as "degraded" (rate-limited) vs "unhealthy" (broken)
- ✅ Better status reporting (healthy / degraded / unhealthy / critical)

**Rationale:** Original version treated rate-limit errors as critical failures, causing false alarms. New version recognizes rate limits as noisy but non-critical.

## New Documentation

### docs/slack-migration-notes.md
- ✅ Documents `dmPolicy` → `dm.policy` migration in OpenClaw 2026.2.14
- ✅ Explains how to run `openclaw doctor --fix` for auto-migration
- ✅ Covers session sprawl prevention (allowlist policies)
- ✅ Includes channel/user ID discovery instructions
- ✅ Provides troubleshooting guide

### docs/model-fallback-strategy.md
- ✅ Explains why Codex-only fallbacks cause cascading failures
- ✅ Documents real-world failure case (4+ hour outage, Feb 2026)
- ✅ Recommends cross-provider fallback pattern
- ✅ Covers alias collision issues and lessons learned
- ✅ Includes testing strategies and cost management
- ✅ Provides monitoring and alerting guidance

## README Updates

- ✅ Bumped version to v2026.02.16
- ✅ Added compatibility note: "Compatible with OpenClaw 2026.2.14+"
- ✅ Added Slack setup to config table
- ✅ Added model fallback strategy doc to docs listing
- ✅ Added new documentation section with comprehensive doc index

## Statistics

```
12 files changed, 719 insertions(+), 113 deletions(-)
```

### Files Modified:
- README.md
- config/compaction-and-pruning.json5
- config/context-overflow-prevention.json5
- config/feature-unlocks.json5
- config/model-aliases-and-fallbacks.json5
- templates/AGENTS.md
- templates/HEARTBEAT.md
- templates/SOUL.md
- scripts/taskrunner/tasks/system_health.py

### Files Created:
- config/slack-setup.json5 (67 lines)
- docs/slack-migration-notes.md (169 lines)
- docs/model-fallback-strategy.md (297 lines)

## Security & Privacy

✅ All changes reviewed for:
- No API keys, tokens, passwords
- No phone numbers, email addresses
- No account IDs or personal identifiers
- All IDs replaced with generic placeholders
- Comments preserved explaining WHY settings exist

## Testing

- ✅ JSON5 syntax manually verified (no trailing commas, balanced braces)
- ✅ All config snippets based on production-tested live config
- ✅ Documentation examples use placeholder values only
- ✅ No breaking changes to existing config structure

## Migration Notes

**For users upgrading from v2026.02.12:**

1. **Slack users:** Review `config/slack-setup.json5` and `docs/slack-migration-notes.md`
2. **Model config:** Consider updating to cross-provider fallbacks per `docs/model-fallback-strategy.md`
3. **Compaction:** Review new memoryFlush prompts in `config/compaction-and-pruning.json5`
4. **Templates:** Re-copy templates if you want the new group chat guidelines

**No immediate action required** — all changes are opt-in config snippets and documentation improvements.

## What's Next (Future Releases)

Potential areas for next release (v2026.02.23):
- Enhanced memory consolidation scripts
- Multi-agent orchestration examples
- Browser automation skill improvements
- Content pipeline automation enhancements

## Contributors

- Production config testing: live deployment since 2026.02.12
- Documentation: lessons learned from real-world failures
- Security review: personal data scrubbing pass

---

**Release prepared by:** OpenClaw Config Maintenance Sub-Agent  
**Release date:** 2026-02-16  
**Staged for review:** Ready for manual review and git commit
