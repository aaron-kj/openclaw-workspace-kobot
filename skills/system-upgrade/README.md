# System Upgrade Management

**Purpose:** Detect, report, and safely execute Node.js and OpenClaw upgrades with human approval.

**Key Features:**
- Weekly Monday 1:00 PM JST update checks
- Risk assessment (Critical/Medium/Low)
- Pre-upgrade backups
- Post-upgrade validation
- Easy rollback procedure
- Human-in-the-loop approval required

**When to Use:**
- Scheduled weekly checks (via HEARTBEAT.md)
- User requests: "check for updates"
- Detecting version mismatches
- Security vulnerability alerts

**Philosophy:** Notify, don't surprise. Assist, don't automate away human judgment.

## Quick Start

### Check for Updates
```bash
# Manual check
source ~/.nvm/nvm.sh
nvm ls-remote --lts | tail -5    # Latest Node LTS
npm view openclaw version         # Latest OpenClaw
```

### Upgrade Process (With Approval)
1. Backup config: `cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup-$(date +%Y%m%d)`
2. Install new Node: `nvm install node`
3. Verify: `openclaw --version && openclaw status`
4. Test: Send a test message
5. Rollback if needed: `nvm use <old-version>`

See `SKILL.md` for detailed procedures.
