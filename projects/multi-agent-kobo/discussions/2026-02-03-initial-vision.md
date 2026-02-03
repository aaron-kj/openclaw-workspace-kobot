# Multi-Agent Kobo: Initial Vision Discussion

**Date:** 2026-02-03  
**Participants:** Aaron (chingchao.wu), Kobot  
**Context:** Slack #kobo-japan-ai-sharing discussion about multi-agent architecture

## 💡 Original Vision

Aaron proposed creating a multi-agent ecosystem with:

1. **AskKobot** - Slack bot for community technical support
2. **ClickUpBot** - Task/project management specialist
3. **MeetingBot** - Weekly townhall meeting summarizer
4. **RHSBot** - HR/internal systems assistant
5. **Kobot** - Personal assistant + ecosystem coordinator

## 🎯 Key Requirements

### Separation of Concerns
- Each bot has specific domain/responsibility
- Clear boundaries prevent scope creep
- Easier to maintain and debug

### Security Isolation
- AskKobot must NOT have access to Aaron's Mac Studio
- Public-facing bot sandboxed from personal data
- Each bot gets only necessary permissions (least privilege)

### Persistent Memory
- Bot "soul" (personality, memory, learnings) persists across restarts
- Workspaces backed by GitHub for version control
- Can rebuild bot from workspace (minus secrets)

### Scalability
- Easy to add new specialist bots
- Standard templates and workflows
- Clear coordination protocol

## 🤔 Key Questions Raised

### Q1: Can multiple bots share GitHub workspace?
**Initial thought:** Share one repo, all contribute to same memory

**Discussion points:**
- Git merge conflicts if multiple bots write simultaneously
- Identity confusion - whose memories are whose?
- Memory contamination - context bleed between bots

**Conclusion:** 
- ❌ Not recommended for identity/memory files (SOUL.md, MEMORY.md)
- ✅ Possible for shared read-only knowledge base
- ✅ Best approach: Separate workspace per bot + shared knowledge repo

### Q2: Can bot "soul" be restored from GitHub?
**Question:** If bot is deleted and respawned, does it remember who it is?

**Answer:** Yes, mostly!
- ✅ Personality (SOUL.md)
- ✅ Long-term memory (MEMORY.md)
- ✅ Past interactions (memory/*.md)
- ✅ Skills and configurations
- ❌ Secrets (must be reconfigured)
- ❌ Current conversation context

**Analogy:** Like waking from a coma - you know who you are and your past, but not the current conversation.

### Q3: GitHub token sharing across bots?
**Question:** Can all bots use same GitHub account?

**Issues:**
- No attribution (can't tell which bot did what)
- Security blast radius
- Rate limiting shared

**Recommendation:**
- ✅ Separate GitHub accounts per bot
- ✅ Or use GitHub App with fine-grained permissions
- ✅ Commits show `git config user.name "BotName"`

## 🏗️ Architecture Decision

**Chosen Model:** Hub-and-Spoke with Isolated Workspaces

**Structure:**
```
┌─────────────────────────┐
│  Shared Knowledge Base  │ (read by all)
└───────────┬─────────────┘
            │
    ┌───────┼───────┐
    │       │       │
┌───▼───┐ ┌─▼───┐ ┌─▼────┐
│ Kobot │ │Ask  │ │Click │ ... (each isolated)
│workspace││Kobot││Up Bot│
└───────┘ └─────┘ └──────┘
            │
    #kobot-coordination (Slack)
```

**Rationale:**
- No git conflicts (each has own repo)
- Clear identity/responsibility
- Secure isolation
- Scalable (easy to add bots)
- Observable coordination (via Slack)

## 📋 Immediate Action Items

1. ✅ Create project folder structure
2. ✅ Document architecture
3. ✅ Write implementation strategy
4. ⬜ Create Docker templates
5. ⬜ Set up #kobot-coordination channel
6. ⬜ Create kobo-knowledge-base repo

## 💭 Open Questions for Next Discussion

1. **Hosting:** Where to run Docker containers?
   - Option A: Mac Studio (local, full control)
   - Option B: Rakuten cloud (production-ready)
   - Option C: External cloud (AWS/GCP)

2. **First Specialist:** MeetingBot or ClickUpBot?
   - MeetingBot: Lower risk, recurring value
   - ClickUpBot: Higher impact, more complex

3. **AskKobot Access:** Public or private repo?
   - Public: Open source, community contribution
   - Private: Keep internal, safer

4. **Monitoring:** What tools for observability?
   - Docker logs + ELK stack?
   - Custom dashboard?
   - Slack notifications?

5. **Budget:** What's acceptable monthly cost?
   - API tokens: $50-100/bot
   - Compute: $20-50 if cloud-hosted
   - Total estimate: $100-200/month

## 🎨 Design Principles Established

1. **Isolation First** - Each bot is independent, no shared state
2. **Least Privilege** - Bots get only necessary permissions
3. **Observable** - All coordination visible (Slack channel)
4. **Recoverable** - Bot state persists in git, can be restored
5. **Scalable** - Standard patterns for adding new bots

## 📝 Quotes

> "If I remove one bot locally/in docker, but keep its remote workspace in GitHub, when I install a new bot in remote/docker, sharing or point to the same workspace that previous bot is using, can this bring the previous bot's soul back?" - Aaron

> "It's like waking up from a coma - you remember who you are and your past, but the current conversation context is gone." - Kobot

## 🔗 Related Documents

- [ARCHITECTURE.md](../ARCHITECTURE.md)
- [STRATEGY.md](../STRATEGY.md)
- [Slack discussion](slack://channel?team=T02878J1S&id=C09AWDW6GDA) (2026-02-03, #kobo-japan-ai-sharing)

---

**Next Discussion:** End of Week 1 - Review Phase 0 progress and decide on Phase 1 details.
