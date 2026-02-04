# Git Identity Setup for Multi-Agent System

## Overview

Each agent in the multi-agent system has its own distinct git identity for proper attribution on GitHub while all repositories remain under the owner's account (aaron-kj).

## Identity Pattern

**Format:** `<AgentName> <<agent-name>+<github-user-id>@users.noreply.github.com>`

This follows GitHub's noreply email pattern with agent-specific prefixes.

## Current Agent Identities

### Kobot (Main Agent)
- **Name:** `Kobot`
- **Email:** `kobot+226668959@users.noreply.github.com`
- **Repository:** https://github.com/aaron-kj/openclaw-workspace-kobot
- **Workspace:** `/Users/chingchao.wu/clawd`
- **Role:** Personal assistant, full system access, coordinates all agents

### SlackKobot (Slack Specialist)
- **Name:** `SlackKobot`
- **Email:** `slackkobot+226668959@users.noreply.github.com`
- **Repository:** https://github.com/aaron-kj/openclaw-workspace-slackkobot
- **Workspace:** `/Users/chingchao.wu/clawd-agents/workspace-slackkobot`
- **Role:** Slack community support for Rakuten Kobo engineering team

## How to Configure Git Identity for a New Agent

### 1. Choose Identity Pattern
```bash
# Pattern: <agent-name>+226668959@users.noreply.github.com
# Example: clickupbot+226668959@users.noreply.github.com
```

### 2. Configure in Agent's Workspace
```bash
cd /path/to/agent/workspace
git config user.name "AgentName"
git config user.email "agentname+226668959@users.noreply.github.com"
```

### 3. Verify Configuration
```bash
git config user.name
git config user.email
```

### 4. Test with a Commit
```bash
# Make a test change
echo "test" >> README.md
git add README.md
git commit -m "Test: Verify git identity"

# Check author in commit log
git log --format="%an <%ae>" -n 1
```

### 5. Push and Verify on GitHub
```bash
git push origin main
# Check GitHub commit view - should show distinct author name
```

## GitHub User ID Reference

**aaron-kj GitHub User ID:** `226668959`

This ID is used in all agent email addresses to ensure commits are associated with the correct account.

## Benefits of This Approach

1. **Clear Attribution**: Each commit shows which agent made the change
2. **Account Security**: All commits still under owner's GitHub account
3. **No Conflicts**: GitHub noreply emails prevent email exposure
4. **Easy Tracking**: Can filter commits by agent using git log
5. **Team Visibility**: Team members can see which agent contributed what

## Filtering Commits by Agent

```bash
# Show all commits by Kobot
git log --author="Kobot"

# Show all commits by SlackKobot
git log --author="SlackKobot"

# Show commit stats by agent
git shortlog -sn --all
```

## Adding New Agents

When creating a new agent:

1. Follow the identity pattern: `<agent-name>+226668959@users.noreply.github.com`
2. Configure git in agent's workspace
3. Create agent's GitHub repository (private or public as needed)
4. Document in this file

### Future Agents (Planned)

- **ClickUpBot**: `clickupbot+226668959@users.noreply.github.com`
- **MeetingBot**: `meetingbot+226668959@users.noreply.github.com`
- **RHSBot**: `rhsbot+226668959@users.noreply.github.com`
- **AskKobot**: `askkobot+226668959@users.noreply.github.com`

## Troubleshooting

### Issue: Commits show as "aaron-kj" instead of agent name

**Cause**: Git identity not configured in agent's workspace

**Solution**:
```bash
cd /path/to/agent/workspace
git config user.name "AgentName"
git config user.email "agentname+226668959@users.noreply.github.com"

# Amend last commit if needed
git commit --amend --reset-author --no-edit
```

### Issue: Email verification warnings

**Cause**: GitHub noreply emails don't need verification

**Solution**: No action needed - this is expected behavior

## Related Documentation

- [Multi-Agent Architecture](../ARCHITECTURE.md)
- [SlackKobot Deployment Plan](./SLACKKOBOT-DEPLOYMENT-PLAN.md)
- [Security Policies](../SECURITY.md)

---

**Last Updated:** 2026-02-04  
**Maintained by:** Kobot
