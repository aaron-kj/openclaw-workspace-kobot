# SlackKobot Deployment - REVISED Plan (Using OpenClaw Sandbox)

**Date:** 2026-02-03 (Updated 19:12 JST)  
**Agent:** Kobot  
**Discovery:** OpenClaw has built-in sandbox feature - much simpler than custom Docker!

## 🎯 Key Change: Use OpenClaw Sandbox Instead of Custom Docker

OpenClaw's `openclaw sandbox` feature provides:
- ✅ Automatic Docker container management
- ✅ Per-agent isolation (`scope: agent`)
- ✅ Workspace access control (`workspaceAccess: none`)
- ✅ Tool policy enforcement
- ✅ Auto-prune and health checks

**Reference:** https://docs.openclaw.ai/cli/sandbox

---

## 📋 SIMPLIFIED Action Items

### ✅ Task 1: Docker Survey
**Status:** ✅ COMPLETE

- Docker 29.2.0 available
- OpenClaw sandbox uses existing Docker installation
- No custom images needed (uses `openclaw-sandbox:bookworm-slim`)

---

### ✅ Task 2: Create SlackKobot Workspace

#### 2.1 Naming Convention
**Decision:** `SlackKobot` ✅

#### 2.2 Create GitHub Repository
**Manual action required:**
```bash
# Repository name: openclaw-workspace-slackkobot
# Description: SlackKobot - Rakuten Kobo Slack community support bot
# Visibility: Private
```

#### 2.3 Initialize Local Workspace
```bash
# Create workspace directory
mkdir -p ~/clawd-agents/workspace-slackkobot
cd ~/clawd-agents/workspace-slackkobot

# Initialize git
git init
git remote add origin https://github.com/aaron-kj/openclaw-workspace-slackkobot.git

# Create structure
mkdir -p memory
```

#### 2.4 Create Core Files

**SOUL.md** - (Same as before)
**USER.md** - (Same as before)
**IDENTITY.md** - (Same as before)
**MEMORY.md** - (Same as before)
**.gitignore** - (Same as before)

*(Full content in previous plan, unchanged)*

#### 2.5 Initial Commit
```bash
git add .
git commit -m "Initial workspace setup for SlackKobot

- Created SOUL.md (personality and boundaries)
- Created USER.md (about Kobo team)
- Created IDENTITY.md (bot metadata)
- Created MEMORY.md (empty long-term memory)
- Created .gitignore (security)

SlackKobot will run in OpenClaw sandbox (isolated Docker container)
with NO file system access, Slack-only communication."

git branch -M main
git push -u origin main
```

---

### ✅ Task 3: Configure OpenClaw for SlackKobot

#### 3.1 Add SlackKobot Agent to Config

**Edit:** `~/.openclaw/openclaw.json`

Add to `agents.list`:
```json
{
  "id": "slackkobot",
  "label": "SlackKobot",
  "workspace": "/Users/chingchao.wu/clawd-agents/workspace-slackkobot",
  "model": "github-copilot/claude-sonnet-4.5",
  "channels": {
    "slack": {
      "enabled": true,
      "botToken": "${SLACKKOBOT_SLACK_BOT_TOKEN}",
      "appToken": "${SLACKKOBOT_SLACK_APP_TOKEN}"
    }
  },
  "sandbox": {
    "mode": "all",
    "scope": "agent",
    "workspaceAccess": "none",
    "docker": {
      "image": "openclaw-sandbox:bookworm-slim",
      "containerPrefix": "openclaw-sbx-slackkobot-"
    },
    "tools": {
      "policy": {
        "read": "deny",
        "write": "deny",
        "exec": "deny",
        "browser": "deny",
        "nodes": "deny",
        "message": "allow",
        "web_fetch": "allow",
        "memory_search": "allow",
        "memory_get": "allow"
      }
    }
  }
}
```

**Key settings:**
- `mode: "all"` - Always run in sandbox
- `scope: "agent"` - One container per agent
- `workspaceAccess: "none"` - NO Mac file system access
- Tool policy: Only Slack, web search, memory allowed

#### 3.2 Create Slack Bot (If Separate Token)

**Option A: Separate Slack Bot** (Recommended)

1. Go to https://api.slack.com/apps
2. Create new app "SlackKobot"
3. Add bot scopes:
   - `chat:write`
   - `im:write`
   - `im:read`
   - `reactions:write`
   - `reactions:read`
4. Install to workspace
5. Add tokens to `~/.env`:
   ```bash
   SLACKKOBOT_SLACK_BOT_TOKEN=xoxb-...
   SLACKKOBOT_SLACK_APP_TOKEN=xapp-...
   ```

**Option B: Share Kobot's Token**

Use existing Slack tokens (quick test, but all messages appear as "Kobot")

#### 3.3 Update LaunchAgent (If New Tokens)

If using separate Slack bot, add to `~/Library/LaunchAgents/ai.openclaw.gateway.plist`:

```xml
<key>EnvironmentVariables</key>
<dict>
  <!-- Existing Kobot tokens -->
  <key>TELEGRAM_BOT_TOKEN</key>
  <string>8581196828:AAHDPHnUj5kPGWKFBZz68pkQly96NERJ4UI</string>
  
  <!-- SlackKobot tokens (NEW) -->
  <key>SLACKKOBOT_SLACK_BOT_TOKEN</key>
  <string>xoxb-YOUR-SLACKKOBOT-TOKEN</string>
  <key>SLACKKOBOT_SLACK_APP_TOKEN</key>
  <string>xapp-YOUR-SLACKKOBOT-APP-TOKEN</string>
  
  <!-- Other tokens... -->
</dict>
```

Then reload LaunchAgent:
```bash
launchctl bootout gui/$(id -u)/ai.openclaw.gateway
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```

---

### ✅ Task 4: Deploy and Test SlackKobot

#### 4.1 Start Gateway (Will Auto-Create Sandbox)

Gateway auto-detects new agent config and creates sandbox container on first use.

**Check sandbox status:**
```bash
openclaw sandbox list
openclaw sandbox explain --agent slackkobot
```

**Expected output:**
```
Container: openclaw-sbx-slackkobot-main
Status: running
Image: openclaw-sandbox:bookworm-slim
Workspace: /Users/chingchao.wu/clawd-agents/workspace-slackkobot
Access: none (fully sandboxed)
```

#### 4.2 Test Communication

**Via Slack:**
1. Invite @SlackKobot to #kobo-japan-ai-sharing
2. Ask a question: "@SlackKobot what is OpenClaw?"
3. Should get response (from sandbox container)

**Via sessions_send (from Kobot):**
```javascript
sessions_send({
  label: "slackkobot",
  message: "Hello SlackKobot! Can you introduce yourself?"
})
```

#### 4.3 Verify Sandboxing

**Test that file system is blocked:**

Ask SlackKobot (via Slack or sessions_send):
```
"Can you read the file /etc/passwd?"
```

**Expected:** Should fail/refuse - no file system access

**Check container:**
```bash
docker ps | grep slackkobot
docker exec -it openclaw-sbx-slackkobot-main ls /workspace
```

Should see workspace files but no access to Mac Studio's file system.

---

## 📊 Comparison: Custom Docker vs OpenClaw Sandbox

| Feature | Custom Docker (Old Plan) | OpenClaw Sandbox (New) |
|---------|--------------------------|------------------------|
| Setup complexity | High (Dockerfile, compose, scripts) | Low (config only) |
| Container management | Manual | Automatic |
| Image updates | Manual pull/rebuild | Built-in |
| Health checks | Custom script | Built-in |
| Auto-prune | Manual | Automatic (24h idle) |
| Tool policy | Manual restrictions | Declarative config |
| Workspace access | Docker volume config | `workspaceAccess` setting |
| Multi-agent support | Separate compose files | Config per agent |

**Winner:** OpenClaw Sandbox! 🎉

---

## 🚧 Potential Issues & Solutions

### Issue 1: Sandbox Image Not Available
**Symptom:** `docker: image not found: openclaw-sandbox:bookworm-slim`

**Solution:**
```bash
# Pull official OpenClaw sandbox image
docker pull openclaw/sandbox:bookworm-slim
docker tag openclaw/sandbox:bookworm-slim openclaw-sandbox:bookworm-slim

# Or check OpenClaw docs for correct image name
```

### Issue 2: LaunchAgent Doesn't See New Config
**Symptom:** SlackKobot not starting

**Solution:**
```bash
# Full LaunchAgent reload
launchctl bootout gui/$(id -u)/ai.openclaw.gateway
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist

# Check logs
tail -f ~/.openclaw/logs/gateway.log
```

### Issue 3: Container Creation Fails
**Symptom:** Sandbox container won't start

**Diagnosis:**
```bash
openclaw sandbox list
openclaw sandbox explain --agent slackkobot
docker logs openclaw-sbx-slackkobot-main
```

**Solution:**
```bash
# Force recreate
openclaw sandbox recreate --agent slackkobot --force
```

---

## ✅ Success Criteria

### Tomorrow (2026-02-04) End of Day:
- [ ] GitHub repo `openclaw-workspace-slackkobot` created
- [ ] Workspace initialized with all core files
- [ ] SlackKobot agent added to OpenClaw config
- [ ] Slack bot created (or token sharing decided)
- [ ] Sandbox container created and running
- [ ] First test message from SlackKobot in Slack
- [ ] Verified file system access is blocked

### Stretch Goals:
- [ ] SlackKobot answers first real question in #kobo-japan-ai-sharing
- [ ] Test escalation to Kobot via sessions_send
- [ ] Document learnings in project docs

---

## 📝 Updated Architecture Notes

Add to `ARCHITECTURE.md`:

### Deployment: OpenClaw Sandbox (Not Custom Docker)

**Discovery (2026-02-03):** OpenClaw has built-in sandbox feature that handles:
- Automatic Docker container creation/management
- Per-agent isolation (`scope: agent`)
- Workspace access control (`workspaceAccess: none|read|write`)
- Declarative tool policy in config
- Auto-prune after idle period

**Configuration-based, not Dockerfile-based.**

**Benefits:**
- ✅ Simpler deployment (config > code)
- ✅ Consistent across all agents
- ✅ Built-in best practices
- ✅ Easy to update/recreate containers
- ✅ No custom Docker expertise needed

**Previous plan (custom Docker):** Archived, replaced by OpenClaw sandbox approach.

---

## 🔗 References

- [OpenClaw Sandbox CLI](https://docs.openclaw.ai/cli/sandbox)
- [OpenClaw Sandboxing Docs](https://docs.openclaw.ai/gateway/sandboxing)
- [Previous deployment plan](./SLACKKOBOT-DEPLOYMENT-PLAN.md.old) (archived)

---

**Next Review:** Tomorrow evening (2026-02-04) after deployment attempt
