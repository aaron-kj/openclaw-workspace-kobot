# Multi-Agent Routing Setup

**Status:** ✅ Active (2026-02-05)

## Overview

OpenClaw supports routing different channels to different agents via `bindings` in the config. This allows one gateway instance to manage multiple specialized agents, each handling specific Slack channels, DMs, or messaging platforms.

## Current Configuration

### Agents
1. **Kobot** (`main`)
   - Model: Claude Sonnet 4.5
   - Workspace: `/Users/chingchao.wu/clawd`
   - Role: Primary assistant, handles personal tasks & AI news
   
2. **SlackKobot** (`slackkobot`)
   - Model: GPT-4o
   - Workspace: `/Users/chingchao.wu/clawd-agents/workspace-slackkobot`
   - Role: Team support bot for Kobo engineering channels
   - Sandboxed (no access to Aaron's personal files)

3. **Aiko-sensei** (`aiko-sensei`)
   - Model: GPT-4o
   - Workspace: `/Users/chingchao.wu/clawd-agents/workspace-aiko-sensei`
   - Role: Japanese teacher (manual sessions only)
   - Sandboxed

### Routing Bindings

```json
{
  "bindings": [
    {
      "agentId": "main",
      "match": {
        "channel": "slack",
        "peer": { "kind": "dm", "id": "D0ACGA44Z6U" }
      }
    },
    {
      "agentId": "main",
      "match": {
        "channel": "slack",
        "peer": { "kind": "channel", "id": "C09AWDW6GDA" }
      }
    },
    {
      "agentId": "main",
      "match": {
        "channel": "telegram"
      }
    },
    {
      "agentId": "slackkobot",
      "match": {
        "channel": "slack",
        "peer": { "kind": "channel", "id": "C0ABFDLC1S7" }
      }
    }
  ]
}
```

**Routing Map:**
- **Kobot handles:**
  - `D0ACGA44Z6U` - Aaron's Slack DM
  - `C09AWDW6GDA` - #kobo-japan-ai-sharing
  - All Telegram messages (@kobo_tokyo_bot)
  
- **SlackKobot handles:**
  - `C0ABFDLC1S7` - #talking-with-clawdbot-test

- **Aiko-sensei:**
  - No automatic routing (manual session only)

## Channel IDs Reference

| Channel | ID | Agent |
|---------|-----|-------|
| Aaron's DM | D0ACGA44Z6U | Kobot |
| #kobo-japan-ai-sharing | C09AWDW6GDA | Kobot |
| #talking-with-clawdbot-test | C0ABFDLC1S7 | SlackKobot |

## How to Update Routing

### 1. Prepare the patch file
Create a JSON file with the updated bindings:
```json
{
  "bindings": [
    // ... your binding rules
  ]
}
```

### 2. Apply the patch
```bash
# Via OpenClaw CLI (recommended)
openclaw gateway config.patch --note "Update routing" --raw "$(cat patch.json)"
```

Or via gateway tool:
```javascript
gateway({
  action: "config.patch",
  note: "Update routing",
  raw: JSON.stringify(patchObject)
})
```

### 3. Verify
- Check logs: `tail -f ~/.openclaw/logs/gateway.log`
- Test by sending messages to each channel
- Look for "delivered reply to channel:XXX" in logs

## Common Issues

### Warning: "slack channels unresolved: D0ACGA44Z6U"
**Status:** Harmless
- Slack can't "resolve" DM channel IDs the same way as named channels
- Routing still works correctly
- This is informational logging, not an error

### Wrong agent responding
**Check:**
1. Verify bindings in config: `openclaw gateway config.get`
2. Check agent logs: `tail -f ~/.openclaw/logs/gateway.log`
3. Ensure channel IDs are correct (case-sensitive)
4. Restart gateway: `openclaw gateway restart`

### Agent not responding
**Check:**
1. Is the channel in allowlist? (`channels.slack.channels`)
2. Does agent have proper bindings?
3. Is gateway running? (`openclaw status`)

## Design Principles

1. **Explicit routing prevents takeovers** - Without explicit bindings, an agent with routing could respond to all channels
2. **One Slack app, multiple agents** - All agents share same bot token (display name: "Kobo Clawdbot Tokyo")
3. **Agents identify themselves** - Each agent introduces itself by name in responses
4. **Sandboxing for team bots** - SlackKobot is sandboxed to prevent access to Aaron's personal files

## Next Steps

- [ ] Test SlackKobot with real Kobo questions
- [ ] Fine-tune SlackKobot's knowledge base
- [ ] Add SlackKobot to #kobo-japan-ai-sharing after testing
- [ ] Consider separate Slack app for SlackKobot (different display name)

## References

- Config schema: `openclaw gateway config.schema`
- Config location: `~/.openclaw/openclaw.json`
- Logs: `~/.openclaw/logs/gateway.log`
- Gateway status: `openclaw status`
