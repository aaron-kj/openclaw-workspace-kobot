# System Upgrade Management Skill

## Purpose
Safely detect, report, and execute Node.js and OpenClaw upgrades with human-in-the-loop approval.

## When to Use This Skill
- Weekly update checks (Monday 1:00 PM JST via heartbeat)
- When user asks "check for updates"
- When detecting version mismatches
- When security vulnerabilities are reported

---

## Detection & Reporting

### Step 1: Check Current Versions

```bash
# Get current Node version
source ~/.nvm/nvm.sh && nvm current

# Get OpenClaw version and location
which openclaw
openclaw --version

# Check where OpenClaw is installed
ls -la ~/.nvm/versions/node/*/lib/node_modules/openclaw 2>/dev/null | grep -o "v[0-9]*\.[0-9]*\.[0-9]*"
```

### Step 2: Check Available Updates

```bash
# Check latest Node LTS
source ~/.nvm/nvm.sh && nvm ls-remote --lts | tail -5

# Check latest Node (including non-LTS)
source ~/.nvm/nvm.sh && nvm ls-remote | tail -5

# Check latest OpenClaw on npm
npm view openclaw version
npm view openclaw time --json | jq -r '.modified'
```

### Step 3: Assess Risk Level

**Version Change Types:**
- **Major (v24 → v25):** 🔴 HIGH RISK - Breaking changes likely
- **Minor (v25.5 → v25.6):** 🟡 MEDIUM RISK - New features, possible issues
- **Patch (v25.6.0 → v25.6.1):** 🟢 LOW RISK - Bug fixes only

**Security Considerations:**
- Check Node.js security releases: https://nodejs.org/en/blog/vulnerability/
- Check OpenClaw changelog: https://github.com/openclaw/openclaw/releases

**Risk Assessment Matrix:**

| Scenario | Risk | Action |
|----------|------|--------|
| Security CVE in current version | 🔴 CRITICAL | Recommend immediate upgrade |
| Major version available | 🟡 MEDIUM | Review changelog, suggest testing |
| Minor version available | 🟢 LOW | Safe to upgrade when convenient |
| Patch version available | 🟢 LOW | Safe to upgrade anytime |
| Current = Latest | ✅ NONE | No action needed |

### Step 4: Generate Report

Send to Aaron's Slack DM with this format:

```markdown
🔍 **Weekly System Update Check** (YYYY-MM-DD)

**Current Environment:**
- Node.js: vX.Y.Z (installed via nvm)
- OpenClaw: YYYY.M.D (located in ~/.nvm/versions/node/vX.Y.Z/)

**Available Updates:**
- Node.js LTS: vA.B.C (current: vX.Y.Z)
- OpenClaw: YYYY.M.D (current: YYYY.M.D)

**Risk Assessment:** 🟢/🟡/🔴
- Node upgrade: [LOW/MEDIUM/HIGH] - [reason]
- OpenClaw upgrade: [LOW/MEDIUM/HIGH] - [reason]

**Recommendation:**
- ✅ Safe to upgrade now
- ⏳ Review changelog first
- ⚠️ Test in isolated environment
- ❌ Wait for patch release

**Changelogs:**
- Node.js: https://github.com/nodejs/node/releases/tag/vA.B.C
- OpenClaw: https://github.com/openclaw/openclaw/releases/tag/YYYY.M.D

Want me to proceed with upgrade? (Reply "yes" to approve)
```

---

## Upgrade Execution (With Approval)

**Prerequisites:**
- User has approved upgrade
- No critical tasks running
- Working hours (9 AM - 10 PM JST preferred)

### Safe Upgrade Process

#### Step 1: Pre-Upgrade Backup

```bash
# Backup OpenClaw config
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup-$(date +%Y%m%d-%H%M%S)

# Record current state
echo "PRE-UPGRADE STATE" > ~/clawd/upgrade-log-$(date +%Y%m%d-%H%M%S).txt
echo "Date: $(date)" >> ~/clawd/upgrade-log-*.txt
source ~/.nvm/nvm.sh && nvm current >> ~/clawd/upgrade-log-*.txt
openclaw --version >> ~/clawd/upgrade-log-*.txt
```

#### Step 2: Execute Upgrade

```bash
# Install new Node (OpenClaw auto-installs via default-packages)
source ~/.nvm/nvm.sh && nvm install node

# Or specific version
source ~/.nvm/nvm.sh && nvm install 25
```

#### Step 3: Post-Upgrade Validation

```bash
# Switch to new version
source ~/.nvm/nvm.sh && nvm use node

# Verify versions
node --version
npm --version
openclaw --version

# Health check
openclaw status

# Test basic functionality
openclaw config get > /dev/null && echo "✅ Config accessible"
```

#### Step 4: Test Critical Functions

```bash
# Check agents
openclaw agents list

# Check channels (if gateway running)
openclaw channels status

# Send test message
# (Manual: Send "test" via Slack/Telegram to verify)
```

#### Step 5: Report Results

Send to Aaron's Slack DM:

```markdown
✅ **Upgrade Complete!**

**Before:**
- Node: vX.Y.Z
- OpenClaw: YYYY.M.D-old

**After:**
- Node: vA.B.C ✅
- OpenClaw: YYYY.M.D-new ✅

**Validation:**
- ✅ OpenClaw starts successfully
- ✅ Config loaded correctly
- ✅ All agents accessible
- ✅ Channels connected
- ✅ Test message sent

**Rollback Available:**
If issues occur, run: `nvm use vX.Y.Z`

Everything looks good! 🚀
```

---

## Rollback Procedure (If Something Breaks)

### Quick Rollback

```bash
# Switch back to old Node version
source ~/.nvm/nvm.sh && nvm use vX.Y.Z  # Replace with previous version

# Verify OpenClaw works
openclaw --version
openclaw status

# Restore config if needed
cp ~/.openclaw/openclaw.json.backup-TIMESTAMP ~/.openclaw/openclaw.json
```

### Report Rollback

```markdown
⚠️ **Upgrade Rolled Back**

**Issue encountered:**
[Describe what went wrong]

**Actions taken:**
- Switched back to Node vX.Y.Z
- Restored config from backup (if applicable)
- Verified system is stable

**Current state:**
- Node: vX.Y.Z (previous version)
- OpenClaw: YYYY.M.D (previous version)
- System: ✅ Stable

**Next steps:**
- Investigate issue
- Report bug if necessary
- Wait for fix before retrying
```

---

## Edge Cases & Troubleshooting

### Version Mismatch Detection

**Scenario:** OpenClaw is in v24 folder, but current Node is v25

```bash
# Detect mismatch
OPENCLAW_PATH=$(which openclaw)
CURRENT_NODE=$(nvm current)

if [[ "$OPENCLAW_PATH" != *"$CURRENT_NODE"* ]]; then
  echo "⚠️ Mismatch detected!"
  echo "OpenClaw: $OPENCLAW_PATH"
  echo "Node: $CURRENT_NODE"
fi
```

**Solution:** 
1. Notify user of mismatch
2. Recommend: `nvm use node` or reinstall OpenClaw in current Node

### Multiple Node Versions with OpenClaw

```bash
# List all OpenClaw installations
find ~/.nvm/versions/node/*/lib/node_modules/openclaw -type d -maxdepth 0 2>/dev/null
```

**Cleanup old versions (optional):**
```bash
# After confirming new version works
nvm uninstall v24.13.0  # Example: remove old Node version
```

---

## Security Monitoring

### Check for CVEs

Manually check:
- Node.js: https://nodejs.org/en/blog/vulnerability/
- npm advisories: `npm audit` (if applicable)
- OpenClaw GitHub: https://github.com/openclaw/openclaw/security/advisories

**If critical CVE found:**
1. Immediate notification to user
2. Mark as 🔴 CRITICAL priority
3. Recommend emergency upgrade
4. Provide CVE details and mitigation

---

## Weekly Check Schedule

**Frequency:** Every Monday at 1:00 PM JST (via HEARTBEAT.md)

**Workflow:**
1. Run detection checks (Steps 1-2)
2. Assess risk (Step 3)
3. Generate report (Step 4)
4. Send to Slack DM
5. Wait for approval
6. Execute if approved (Steps 1-5 of upgrade)
7. Update memory/heartbeat-state.json with check timestamp

---

## Memory Tracking

Store in `memory/heartbeat-state.json`:

```json
{
  "lastChecks": {
    "systemUpdate": 1738732800,
    "lastNodeVersion": "v25.6.0",
    "lastOpenClawVersion": "2026.2.2-3",
    "lastCheckResult": "up-to-date"
  }
}
```

Update after each check to prevent duplicate notifications.

---

## Human-in-the-Loop Approval

**NEVER upgrade without explicit approval:**
- User must reply "yes" / "proceed" / "upgrade" to Slack notification
- Timeout: If no response within 24 hours, skip this week
- Emergency: Critical CVE = ask immediately, don't wait for Monday

**Approval Detection:**
- Look for user message containing: "yes", "proceed", "upgrade", "do it"
- In Slack DM channel (D0ACGA44Z6U)
- Within reasonable timeframe of report

---

## Notes

- **Auto-upgrade disabled by default** - Always require human approval
- **Test in isolation first** - For major version upgrades, suggest testing in separate environment
- **Keep rollback simple** - `nvm use <old-version>` is the escape hatch
- **Document everything** - Log all upgrades in memory/YYYY-MM-DD.md

**Philosophy:** "Notify, don't surprise. Assist, don't automate away human judgment."
