# 🔒 AI Agent Posting Approval Gate

## Overview

Technical safeguard system that makes it **physically impossible** for the AI agent to post to social media (X/Twitter, LinkedIn, etc.) without explicit human approval. This is infrastructure-level enforcement, not behavioral rules.

## Problem Statement

- Agent has behavioral rule: "draft to Discord, wait for approval"
- No TECHNICAL enforcement — malfunction or prompt injection could bypass rules
- Need system where posting is impossible without human approval
- Must preserve read access (Bird CLI search/read still works)

## Architecture

```
Agent Request → Gateway Tool Check → BLOCKED (no approval token)
                    ↓
Draft to Discord → Human Approval (✅ reaction) → Webhook → Issue Token
                    ↓
Agent Retry → Gateway Check → ALLOWED (valid token) → Post to X
```

## Components

### 1. Gateway Tool Restrictions (`gateway-config.json`)
- Blocks standard `message` tool for social platforms
- Adds approval-gated `message_approved` variant
- Requires valid approval token in request

### 2. Discord Approval Server (`approval-server.js`)
- Webhook server listening for Discord reactions
- ✅ reaction → Generate one-time approval token
- ❌ reaction → Deny request
- Embeds show draft content with approve/deny buttons

### 3. Credential Broker (`token-broker.js`)
- Issues one-time tokens after human approval
- Tokens expire after single use or timeout (5 minutes)
- Maintains audit trail of all approvals

### 4. Agent Tool Wrapper (`approved-message-tool.js`)
- Modified message tool that checks for approval token
- Falls back to draft-to-Discord if no approval
- Handles token refresh and retry logic

## Security Properties

✅ **Physically impossible** to post without approval (tool blocked at gateway)  
✅ **One-time tokens** - Cannot be reused or cached by agent  
✅ **Fail-secure** - Default deny, must explicitly approve each post  
✅ **Audit trail** - All requests, approvals, and posts logged  
✅ **Platform extensible** - Works for X, LinkedIn, any social platform  
✅ **Read access preserved** - Research tools still work  

## Installation

1. **Configure Gateway Tool Restrictions**
   ```bash
   # Backup current config
   cp ~/.clawdbot/clawdbot.json ~/.clawdbot/clawdbot.json.backup
   
   # Apply posting restrictions
   node scripts/posting-gate/install-restrictions.js
   ```

2. **Start Approval Server**
   ```bash
   cd ~/clawd/scripts/posting-gate
   npm install
   node approval-server.js
   ```

3. **Test System**
   ```bash
   # This should be blocked and draft to Discord
   echo "Test posting system" | clawdbot message send --target twitter
   ```

## Usage

1. Agent attempts to post → Gets blocked → Drafts to Discord
2. Discord shows content with ✅ and ❌ buttons
3. Human clicks ✅ to approve or ❌ to deny
4. If approved, agent automatically retries and post succeeds
5. Token expires after use or 5 minutes

## Extension

Add new platforms by:
1. Adding platform to `RESTRICTED_PLATFORMS` in config
2. Adding platform-specific credential handling in broker
3. Testing approval workflow

## Monitoring

- All approval requests logged to `~/clawd/scripts/posting-gate/logs/`
- Failed posting attempts logged with reasons
- Weekly reports on approval patterns

## Emergency Override

```bash
# Temporary disable (for emergencies only)
node scripts/posting-gate/emergency-override.js --disable

# Re-enable restrictions  
node scripts/posting-gate/emergency-override.js --enable
```

**⚠️ Warning:** Override should only be used in emergencies and immediately re-enabled.