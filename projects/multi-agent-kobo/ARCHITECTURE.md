# Multi-Agent Kobo: Architecture Design

**Version:** 0.1  
**Last Updated:** 2026-02-03  
**Status:** Draft

## 🏗️ System Architecture

### Hub-and-Spoke Model

```
                    ┌─────────────────────────────┐
                    │   GitHub: Knowledge Base    │
                    │   (Read-only, shared docs)  │
                    └──────────────┬──────────────┘
                                   │
                    ┌──────────────┼──────────────┐
                    │              │              │
            ┌───────▼──────┐ ┌────▼─────┐ ┌─────▼──────┐
            │   AskKobot   │ │ClickUp   │ │  Meeting   │
            │  (Docker)    │ │   Bot    │ │    Bot     │
            └───────┬──────┘ └────┬─────┘ └─────┬──────┘
                    │              │              │
                    └──────────────┼──────────────┘
                                   │
                            ┌──────▼──────┐
                            │    Kobot    │
                            │ (Mac Studio)│
                            │ Coordinator │
                            └─────────────┘
                                   │
                    Slack: #kobot-coordination
```

## 🤖 Agent Specifications

### Kobot (Hub/Coordinator)
**Role:** Personal work assistant + Multi-agent coordinator  
**Location:** Mac Studio (local)  
**Access:** Full system access, all tools, personal data  
**Responsibilities:**
- Manage Aaron's daily work tasks
- Coordinate other agents
- Handle sensitive personal operations
- Delegate tasks to specialists

**Communication:**
- Telegram (personal DM)
- Slack (DMs + coordination channel)

### AskKobot (Community Bot)
**Role:** Public Q&A bot for Kobo engineers  
**Location:** Docker container (isolated sandbox)  
**Access:** Slack only, NO file system, NO personal data  
**Responsibilities:**
- Answer technical questions in #kobo-japan-ai-sharing
- Provide Kobo domain knowledge
- Learn from community interactions
- Escalate complex queries to Kobot

**Security:**
- Read-only Slack access to allowed channels
- No exec tools, no file system access
- Web search only for external information
- Stateless or minimal memory (public interactions only)

### ClickUpBot (Task Manager)
**Role:** Project/task management specialist  
**Location:** Docker or Mac Studio (TBD)  
**Access:** ClickUp API only  
**Responsibilities:**
- Create/update tasks based on requests
- Generate project reports
- Track deadlines and priorities
- Sync task status with team

### MeetingBot (Summarizer)
**Role:** Meeting transcription and summarization  
**Location:** Docker (scheduled job)  
**Access:** Meeting recordings (Zoom/Google Meet API)  
**Responsibilities:**
- Weekly townhall meeting summaries
- Extract action items
- Distribute summaries to team
- Archive historical meeting data

### RHSBot (HR Assistant)
**Role:** HR/internal systems helper  
**Location:** Docker (high security)  
**Access:** Rakuten internal APIs (if available)  
**Responsibilities:**
- Answer HR policy questions
- Handle leave requests
- Provide benefits information
- Internal tool guidance

## 🔄 Inter-Agent Communication

### Method: Slack Coordination Channel

**Channel:** `#kobot-coordination` (private)  
**Members:** All bots + Aaron

**Protocol:**
1. Agent posts request in structured format
2. Coordinator (Kobot) acknowledges
3. Appropriate specialist responds
4. Results posted back to channel
5. Original requester notified

**Message Format:**
```
🔵 REQUEST from: AskKobot
📌 Task: Create ClickUp task for feature request
📄 Details: User @john requested API rate limit docs
🎯 Assign to: ClickUpBot
```

### Alternative: Direct Sessions (Future)

Use OpenClaw's `sessions_send` for direct agent-to-agent communication without Slack intermediary.

## 📊 Data Architecture

### Isolated Workspaces

Each agent has its own GitHub repository:
- `aaron-kj/kobot-workspace` (private)
- `aaron-kj/askkobot-workspace` (private/public TBD)
- `aaron-kj/clickupbot-workspace` (private)
- `aaron-kj/meetingbot-workspace` (private)
- `aaron-kj/rhsbot-workspace` (private)

### Shared Knowledge Base

Repository: `aaron-kj/kobo-knowledge-base`

**Structure:**
```
kobo-knowledge-base/
├── README.md
├── tech-stack/
│   ├── backend.md
│   ├── frontend.md
│   └── infrastructure.md
├── processes/
│   ├── deployment.md
│   ├── code-review.md
│   └── incident-response.md
├── faqs/
│   └── common-issues.md
└── team/
    └── contacts.md
```

**Access:**
- Read: All bots
- Write: Via Pull Request only
- Curated by Kobot

## 🔐 Security Model

### Principle: Least Privilege

Each agent gets ONLY what it needs:

| Agent       | File System | Personal Data | External APIs | Internal APIs |
|-------------|-------------|---------------|---------------|---------------|
| Kobot       | ✅ Full      | ✅ Full        | ✅ All         | ✅ All         |
| AskKobot    | ❌ None      | ❌ None        | ✅ Web Search  | ❌ None        |
| ClickUpBot  | ❌ None      | ⚠️ Minimal     | ✅ ClickUp     | ❌ None        |
| MeetingBot  | ❌ None      | ❌ None        | ✅ Zoom/Meet   | ❌ None        |
| RHSBot      | ❌ None      | ⚠️ HR Data     | ❌ None        | ✅ RHS         |

### Credential Management

**Per-agent secrets:**
- Stored in Docker secrets or environment variables
- NOT in git repositories
- Rotated independently
- Separate GitHub/Slack accounts per bot

**Audit trail:**
- Each bot commits with own identity
- Slack actions logged per bot
- API calls attributed to specific bot token

## 🚀 Deployment Architecture

### Kobot: Native (Mac Studio)
- LaunchAgent service
- Full system access
- Always running

### Other Bots: Docker Containers
- Isolated environments
- Defined resource limits
- Easy restart/rebuild
- Portable to cloud if needed

**Deployment options:**
1. **Local Docker** - On Mac Studio (development/testing)
2. **Rakuten Cloud** - Internal infrastructure (production)
3. **External Cloud** - AWS/GCP (if Rakuten unavailable)

## 📈 Scaling Strategy

### Horizontal Scaling
- Add more specialist bots as needed
- Each new bot follows same pattern:
  1. Create isolated workspace
  2. Define security boundaries
  3. Deploy in Docker
  4. Add to coordination channel

### Vertical Scaling
- Enhance individual bot capabilities
- Add more tools/skills
- Improve memory/knowledge base

## 🔮 Future Enhancements

1. **Shared Memory Search** - Cross-agent knowledge queries
2. **Task Marketplace** - Bots bid on tasks they can handle
3. **Learning Pipeline** - AskKobot contributions feed knowledge base
4. **Monitoring Dashboard** - Real-time agent status/metrics
5. **Agent Templating** - Standard bot creation workflow

## 📝 Open Questions

1. Where to host Docker containers? (Mac Studio vs Rakuten cloud)
2. How to handle bot authentication with Rakuten internal systems?
3. Should AskKobot workspace be public or private?
4. Monitoring/alerting strategy for production bots?
5. Cost implications of running multiple agents?

---

**Next Steps:** See [STRATEGY.md](./STRATEGY.md) for implementation roadmap.
