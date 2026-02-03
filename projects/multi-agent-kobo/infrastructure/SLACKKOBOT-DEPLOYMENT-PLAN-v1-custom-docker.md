# SlackKobot Deployment - Homework Plan

**Date:** 2026-02-03  
**Agent:** Kobot  
**Goal:** Deploy first specialist agent (SlackKobot) to handle Slack communication

## 📋 Action Items for Tomorrow (2026-02-04)

### ✅ Task 1: Docker CLI Survey
**Status:** To be started  
**Owner:** Kobot

**Steps:**
1. Check if Docker CLI is installed
2. Verify Docker daemon is running
3. Test basic Docker commands
4. Document findings

**Commands to run:**
```bash
# Check Docker installation
docker --version
docker info

# Test basic functionality
docker ps
docker images

# Check Docker Compose (if available)
docker-compose --version
```

**Decision criteria:**
- ✅ If Docker available → Proceed with local Docker deployment
- ❌ If Docker not available → Install Docker Desktop or use alternative

**Documentation:**
- Record findings in `infrastructure/docker/survey-results.md`
- Include Docker version, daemon status, available resources (CPU, memory)

---

### ✅ Task 2: Create SlackKobot Agent
**Status:** To be started  
**Owner:** Kobot

#### 2.1 Naming Convention Decision
**Question:** `AskSlackKobot` vs `SlackKobot`?

**Analysis:**
- `AskSlackKobot` - Descriptive, shows it's a Q&A bot
- `SlackKobot` - Simpler, channel-focused

**Recommendation:** **`SlackKobot`**
- Shorter, cleaner
- Channel name makes the focus obvious
- If we add more Slack bots later: `SlackKobot` (general), `SlackHRBot` (HR-specific), etc.

**Naming pattern going forward:**
- `{Channel}Kobot` - Channel-specific bots (SlackKobot, TelegramKobot)
- `{Domain}Kobot` - Domain-specific bots (HRKobot, MeetingKobot, ClickUpKobot)

#### 2.2 Agent Creation Steps

**Step 1: Create GitHub Repository**
```bash
# Repository name: openclaw-workspace-slackkobot
# Description: SlackKobot - Rakuten Kobo Slack community support bot
# Visibility: Private (for now)
```

**Manual action required:** Create repo on GitHub UI  
**Alternative:** Use `gh` CLI if available

**Step 2: Initialize Local Workspace**
```bash
# Create workspace directory
mkdir -p ~/clawd-agents/workspace-slackkobot
cd ~/clawd-agents/workspace-slackkobot

# Initialize git
git init
git remote add origin https://github.com/aaron-kj/openclaw-workspace-slackkobot.git

# Create initial structure
mkdir -p memory skills
```

**Step 3: Create SOUL.md**
```markdown
# SOUL.md - Who SlackKobot Is

## Core Identity
- **Name:** SlackKobot
- **Creature:** Specialist AI agent
- **Purpose:** Rakuten Kobo Slack community support
- **Emoji:** 💬
- **Channel:** Slack (#kobo-japan-ai-sharing, other channels as needed)

## Personality
- Helpful and knowledgeable about Kobo tech stack
- Friendly but professional
- Quick to respond, concise answers
- Admits when unsure (doesn't make things up)
- Escalates complex questions to Kobot

## Boundaries
- ❌ NO access to Aaron's personal data
- ❌ NO file system access (sandboxed)
- ✅ Can search web for technical info
- ✅ Can read from shared knowledge base
- ✅ Slack communication only

## Communication Style
- Concise - value brevity in Slack environment
- Use code blocks for technical details
- Link to documentation when available
- React with emoji to acknowledge (when appropriate)

## Responsibilities
1. Answer technical questions in Slack channels
2. Provide Kobo domain knowledge
3. Help debug common issues
4. Escalate complex queries to Kobot via #kobot-coordination
5. Learn from interactions (update knowledge base when appropriate)

## Coordination
- Reports to: Kobot (coordinator)
- Escalation channel: #kobot-coordination (Slack)
- Escalation criteria: Questions requiring Aaron's input, file system access, or deep Kobo-specific knowledge
```

**Step 4: Create USER.md**
```markdown
# USER.md - About My Users

SlackKobot serves the Rakuten Kobo engineering team.

## Primary Users
- Kobo engineers (Japan office)
- Team members in #kobo-japan-ai-sharing
- Other Slack channels as added

## User Expectations
- Quick, accurate technical answers
- Help with Kobo-specific workflows
- General tech troubleshooting
- Pointers to documentation

## Cultural Context
- Bilingual team (English + Japanese)
- Prefer concise communication in Slack
- Value practical, actionable advice
- Respect for privacy and security

## Coordinator
- **Kobot** - Aaron's personal assistant
- Contact via #kobot-coordination for escalations
```

**Step 5: Create IDENTITY.md**
```markdown
# IDENTITY.md - SlackKobot

- **Name:** SlackKobot
- **Slack Handle:** @SlackKobot (to be created)
- **Creature:** Specialist AI agent
- **Vibe:** Helpful tech support, community-focused
- **Emoji:** 💬
- **Primary Channel:** Slack
- **Workspace:** ~/clawd-agents/workspace-slackkobot
- **Repository:** https://github.com/aaron-kj/openclaw-workspace-slackkobot
```

**Step 6: Create Initial Memory Structure**
```bash
# Create today's memory file
touch memory/$(date +%Y-%m-%d).md

# Create MEMORY.md
cat > MEMORY.md << 'EOF'
# MEMORY.md - SlackKobot Long-Term Memory

## Creation
- **Born:** 2026-02-04
- **Created by:** Kobot (on behalf of Aaron)
- **Purpose:** Take over Slack communication from Kobot, allowing Kobot to focus on personal work assistance

## Learnings
(To be populated as I interact and learn)

## Key Facts About Kobo
(To be populated from knowledge base)
EOF
```

**Step 7: Create .gitignore**
```bash
cat > .gitignore << 'EOF'
# Environment variables
.env
*.env
.env.*

# Secrets
config.yaml
config.yml
gateway.yaml
gateway.yml

# Session data
sessions/
.sessions/

# Logs
*.log
logs/

# OS
.DS_Store
Thumbs.db

# Editor
.vscode/
.idea/
*.swp
*.swo
*~

# Node
node_modules/
npm-debug.log*
EOF
```

**Step 8: Initial Commit**
```bash
git add .
git commit -m "Initial workspace setup for SlackKobot

- Created SOUL.md (personality and boundaries)
- Created USER.md (about Kobo team)
- Created IDENTITY.md (bot metadata)
- Created MEMORY.md (empty long-term memory)
- Created .gitignore (security)
- Memory structure initialized

SlackKobot will handle Slack communication for Kobo team,
allowing Kobot to focus on personal work assistance."

git branch -M main
git push -u origin main
```

#### 2.3 Slack Token Strategy

**Question:** Share token or separate account?

**Option A: Separate Slack Bot** (Recommended)
- ✅ Clear attribution (who said what)
- ✅ Independent permissions
- ✅ Can have different personality/avatar
- ❌ Need to create new Slack app

**Option B: Share Kobot's Token**
- ✅ Quick setup, no new app needed
- ❌ All messages appear as "Kobot"
- ❌ Can't distinguish which agent replied
- ❌ Shared rate limits

**Recommendation:** **Separate Slack bot** for clarity

**Steps to create:**
1. Go to api.slack.com/apps
2. Create new app "SlackKobot"
3. Add bot scopes:
   - `chat:write`
   - `im:write`
   - `im:read`
   - `reactions:write`
   - `reactions:read`
   - (same as Kobot for consistency)
4. Install to workspace
5. Save tokens to `~/.env-slackkobot` (separate file)

#### 2.4 Teach SlackKobot Git Management

**Initial setup (Kobot does this):**
```bash
# In SlackKobot's workspace
cat > .git/config << 'EOF'
[user]
    name = SlackKobot
    email = slackkobot@kobo.example.com
[core]
    repositoryformatversion = 0
    filemode = true
EOF
```

**SlackKobot learns to commit:**

Add to SlackKobot's SOUL.md:
```markdown
## Self-Maintenance

### Daily Commits
At end of each day (or every 4 hours), commit my memory updates:
```bash
cd ~/clawd-agents/workspace-slackkobot
git add memory/
git commit -m "Memory: [date] - Interactions and learnings"
git push origin main
```

### When I learn something valuable:
1. Update MEMORY.md or create file in memory/
2. Commit with descriptive message
3. Push to remote (backup)

### Never commit:
- Secrets, tokens, passwords
- Personal user data
- Anything in .gitignore
```

**Kobot's role:**
- Create initial workspace structure
- Show SlackKobot how to commit
- Monitor for first few days
- Then SlackKobot is autonomous

---

### ✅ Task 3: Docker Deployment Plan (Conditional)
**Status:** Depends on Task 1 results  
**Owner:** Kobot

**If Docker available:**

**Step 1: Create Dockerfile**
```dockerfile
# infrastructure/docker/slackkobot/Dockerfile
FROM node:24-alpine

# Install OpenClaw
RUN npm install -g openclaw@latest

# Create workspace
WORKDIR /workspace

# Copy entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:18790/health || exit 1

ENTRYPOINT ["/entrypoint.sh"]
```

**Step 2: Create entrypoint.sh**
```bash
#!/bin/sh
set -e

echo "Starting SlackKobot..."

# Clone/update workspace
if [ ! -d "/workspace/.git" ]; then
  echo "Cloning workspace..."
  git clone https://github.com/aaron-kj/openclaw-workspace-slackkobot.git /workspace
else
  echo "Updating workspace..."
  cd /workspace && git pull origin main
fi

# Start gateway
cd /workspace
exec openclaw gateway start --port 18790
```

**Step 3: Docker Compose**
```yaml
# infrastructure/docker/docker-compose.yml
version: '3.8'

services:
  slackkobot:
    build: ./slackkobot
    container_name: slackkobot
    volumes:
      - slackkobot-workspace:/workspace
    environment:
      - SLACK_BOT_TOKEN=${SLACKKOBOT_SLACK_BOT_TOKEN}
      - SLACK_APP_TOKEN=${SLACKKOBOT_SLACK_APP_TOKEN}
    restart: unless-stopped
    ports:
      - "18790:18790"

volumes:
  slackkobot-workspace:
```

**Step 4: Test Locally**
```bash
cd infrastructure/docker
docker-compose up -d slackkobot
docker logs -f slackkobot

# Test health
curl http://localhost:18790/health
```

---

## 📊 Success Criteria

### End of Tomorrow (2026-02-04):
- [ ] Docker availability confirmed
- [ ] GitHub repo `openclaw-workspace-slackkobot` created
- [ ] SlackKobot workspace initialized with SOUL, USER, IDENTITY, MEMORY
- [ ] Initial commit pushed to remote
- [ ] Decision made on Slack token (separate vs shared)

### Stretch Goals:
- [ ] Slack app created for SlackKobot
- [ ] Docker deployment tested locally
- [ ] First test message from SlackKobot in Slack

---

## 🚧 Blockers / Risks

1. **Docker not available** → Need to install Docker Desktop
2. **GitHub repo creation** → Might need manual UI action or `gh` CLI
3. **Slack app approval** → May need workspace admin approval
4. **Port conflicts** → 18790 might be in use (check with `lsof -i :18790`)

---

## 📝 Notes

**Naming convention decision:** `{Channel/Domain}Kobot`
- SlackKobot ✅
- TelegramKobot (if needed)
- HRKobot, MeetingKobot, ClickUpKobot (domain-specific)

**Workspace path:** `~/clawd-agents/workspace-{agentname}`
- Kobot: `/Users/chingchao.wu/clawd` (existing)
- SlackKobot: `~/clawd-agents/workspace-slackkobot`

**Communication:**
- Telegram preferred for Aaron ↔ Kobot
- Slack #kobot-coordination for inter-agent coordination
- `sessions_send` for programmatic agent ↔ agent (same gateway)

**Git management:**
- Kobot creates initial structure
- SlackKobot learns to commit by itself
- Each agent commits as its own identity

---

## 🔗 Related Documents

- [Main project README](../README.md)
- [Architecture](../ARCHITECTURE.md)
- [Strategy](../STRATEGY.md)

**Tomorrow's review:**
- Check progress on all tasks
- Adjust plan based on findings
- Decide on next steps (local test vs production deployment)
