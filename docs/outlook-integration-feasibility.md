# Outlook/MS365 Integration Feasibility (via Microsoft Graph API)

**Source:** Jojo's research (Task #009)  
**Date:** 2026-02-01  
**Status:** Research complete, awaiting decision

---

## TL;DR - Can Kobot Book Meetings?

**Short answer:** YES, technically feasible!

**Reality:** Requires compliance approvals first (PRA assessment, security audit).

**Timeline:**
- **PoC (Aaron only):** 1-2 weeks
- **With approvals:** 4-6 weeks  
- **Team-wide:** 4-8 weeks

---

## Two Scenarios

### Scenario A: Aaron Only (Recommended to Start)
- ✅ **Delegated permissions** (user context)
- ✅ **No admin consent** required
- ✅ Minimal scope: `Mail.Read`, `Mail.Send`, `Calendars.ReadWrite`
- ✅ Can upgrade to Scenario B later
- ⏱️ Faster to implement

### Scenario B: Team-Wide
- 🔐 **Application permissions** (app context)
- ⚠️ **Requires admin consent**
- 📋 More extensive approval process
- 🎯 Better for team-wide automation

---

## What Kobot Could Do

Once approved and integrated:

### Email Capabilities
- ✅ Read inbox (check for urgent messages)
- ✅ Send emails on your behalf
- ✅ Reply to threads
- ✅ Summarize email threads
- ✅ Filter/categorize emails

### Calendar Capabilities
- ✅ Read your calendar
- ✅ Create calendar events
- ✅ Book meeting rooms (via resource attendees)
- ✅ Find available time slots
- ✅ Send meeting invites
- ✅ Update/cancel meetings

### Meeting Room Booking
- ✅ Query room availability
- ✅ Book rooms as resource attendees
- ✅ Handle conflicts/reschedule

---

## Technical Approach

### Architecture
1. **OAuth2 authentication** - Standard Microsoft flow
2. **Token storage** - Secure key vault (e.g., Vaultwarden)
3. **API calls** - Microsoft Graph API endpoints
4. **Real-time updates** - Webhooks + delta queries (optional)

### Minimum Permissions Needed (Scenario A)
```
Mail.Read           - Read Aaron's emails
Mail.Send           - Send on Aaron's behalf
Calendars.ReadWrite - Manage calendar and book rooms
```

### Integration Points
- Use existing OpenClaw OAuth skill pattern
- Store tokens securely (similar to Slack/Telegram tokens)
- Skill-based approach: `outlook` skill for email/calendar operations

---

## Compliance Requirements (Rakuten)

### Before Implementation:

1. **App Registration** - Register in Azure AD/Entra admin center
2. **AI Policy Review** - Submit for PRA (Privacy Risk Assessment)
3. **Security Audit** - Comply with audit requirements
4. **Data Retention** - Define retention/deletion schedule
5. **Admin Consent** - Only if using Application permissions (Scenario B)

### Internal Resources Available:
- Microsoft Graph API利用の流れ_v2 (setup guide)
- AI Code of Ethics & User Guidelines
- Rakuten AI Agent Onboarding Guide
- Security_Audit_Operation_Manual
- Kobo-Data-Retention-Policy-v1.1

---

## Implementation Plan

### Phase 1: PoC (1-2 weeks)
**Goal:** Prove technical feasibility with Aaron only

**Steps:**
1. Register app in Entra admin center
2. Configure Delegated permissions (minimal set)
3. Implement OAuth2 flow
4. Build basic features:
   - Read emails
   - Create calendar events
   - Book meeting room (test)
5. Test with Aaron's account

**Deliverable:** Working demo

---

### Phase 2: Compliance & Production (4-6 weeks)
**Goal:** Get approvals and deploy properly

**Steps:**
1. Submit for AI policy review (PRA assessment)
2. Security audit compliance check
3. Document data handling/retention
4. Production deployment
5. Monitor and iterate

**Deliverable:** Approved, production-ready integration

---

### Phase 3: Team Expansion (Optional, 4-8 weeks)
**Goal:** Enable for entire team

**Steps:**
1. Evaluate need for Application permissions
2. Request admin consent (if needed)
3. Extend to team members
4. Create team documentation
5. Training/onboarding

**Deliverable:** Team-wide capability

---

## Risk Assessment

### Technical Risk: 🟢 LOW
- ✅ Well-documented API
- ✅ Standard OAuth2 flow
- ✅ Clear implementation path
- ✅ Microsoft has good SDKs/examples

### Compliance Risk: 🟡 MEDIUM
- ⚠️ Requires multiple approvals
- ⚠️ AI policy review needed
- ⚠️ Security audit requirements
- **Mitigation:** Start with Scenario A (minimal scope)

### Timeline Risk: 🟡 MEDIUM
- ⏱️ Approvals can take 4-6 weeks
- ⏱️ PoC is quick (1-2 weeks)
- **Mitigation:** Start PoC while approvals progress

---

## Decision Needed from Aaron

**Question:** Which scenario should we pursue?

### Option A: Aaron Only (Recommended)
**Pros:**
- ✅ Faster (no admin consent)
- ✅ Lower compliance burden
- ✅ Can test and prove value first
- ✅ Reversible (can upgrade later)

**Cons:**
- ❌ Only works for Aaron
- ❌ Can't automate team-wide tasks

### Option B: Team-Wide
**Pros:**
- ✅ Enables team automation
- ✅ More powerful capabilities
- ✅ Better ROI for team

**Cons:**
- ❌ Requires admin consent
- ❌ More extensive approvals
- ❌ Longer timeline
- ❌ Higher compliance overhead

---

## Answering Team Questions

### "Can Kobot book a meeting for me?"

**Answer:**
> "Yes, technically! Kobot can integrate with Outlook via Microsoft Graph API to:
> - Check your calendar
> - Find available times
> - Book meeting rooms
> - Send invites
> 
> **However,** we need to complete compliance approvals first (PRA assessment, security audit). 
> 
> **Timeline:**
> - PoC demo: 1-2 weeks
> - With approvals: 4-6 weeks
> 
> We can start with Aaron's account (Scenario A) to prove it works, then expand if there's team interest!"

### "Why does it take so long?"

**Answer:**
> "The technical integration is quick (1-2 weeks for PoC). The 4-6 week timeline is for Rakuten's compliance process:
> - AI policy review (PRA assessment)
> - Security audit compliance
> - Data retention policy documentation
> 
> This is standard for any AI agent accessing company data. The good news is we can start building the PoC while approvals are in progress!"

### "Can it work for the whole team?"

**Answer:**
> "Yes! There are two approaches:
> 
> **Scenario A (easier):** Each person authorizes Kobot for their account (like connecting Slack)
> - No admin consent needed
> - Each user opts in
> 
> **Scenario B (more powerful):** Team-wide application permissions
> - Requires admin consent
> - Longer approval process
> 
> We recommend starting with Scenario A to prove value, then expanding if there's demand!"

---

## Related Capabilities

Once Outlook integration is in place, we can also:

### 1. Email Automation
- "Summarize emails from today"
- "Draft reply to [person]"
- "Find that email about [topic]"

### 2. Calendar Intelligence
- "When is my next meeting?"
- "Find a 30-min slot this week for [person]"
- "What's on my calendar tomorrow?"

### 3. Meeting Room Management
- "Book Conference Room A for 2pm tomorrow"
- "Find an available room for 10 people"
- "Check if Room B is free now"

### 4. Cross-Channel Integration
- Get calendar reminders in Slack/Telegram
- Book meetings from chat
- Email summaries in daily digest

---

## Resources & References

### Jojo's Research
- Full plan: 522 lines of detailed analysis
- Internal Rakuten guides obtained
- MS365 Copilot consultation results

### Microsoft Documentation
- [Microsoft Graph API](https://docs.microsoft.com/en-us/graph/)
- [Outlook API](https://docs.microsoft.com/en-us/graph/outlook-overview)
- [Calendar API](https://docs.microsoft.com/en-us/graph/api/resources/calendar)

### Internal Rakuten Guides (Available)
- Microsoft Graph API利用の流れ_v2
- AI Code of Ethics & User Guidelines
- Rakuten AI Agent Onboarding Guide
- Security_Audit_Operation_Manual
- Kobo-Data-Retention-Policy-v1.1

---

## Next Steps

### If Aaron wants to proceed:

1. **Decide:** Scenario A or B?
2. **Register app** in Entra admin center (with IT help)
3. **Start PoC** (can begin immediately)
4. **Submit for approvals** (parallel track)
5. **Test with Aaron** (1-2 weeks)
6. **Get compliance approval** (4-6 weeks)
7. **Production deployment**

### If team shows interest:

1. Gauge interest in #kobo-japan-ai-sharing
2. Collect use cases (what would people want?)
3. Plan team rollout (Scenario A vs B)
4. Create documentation/training materials

---

## Lessons Learned (from Jojo)

### Positive Findings:
- ✅ Rakuten has established processes for Graph API
- ✅ Internal guides exist and are helpful
- ✅ MS365 Copilot is great for enterprise context
- ✅ Technical path is clear and well-documented

### Important Notes:
- ⚠️ AI agent use cases require PRA assessment
- ⚠️ Security audit compliance is mandatory
- ⚠️ Start small, prove value, then expand
- ⚠️ Compliance-first approach avoids future issues

---

**Last Updated:** 2026-02-05 (based on Jojo's 2026-02-01 research)  
**Next Review:** After Aaron decides on Scenario A vs B  
**Contact:** Ask Aaron about Jojo's detailed notes if needed
