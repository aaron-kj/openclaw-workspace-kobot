# Multi-Agent Kobo Architecture Project

**Status:** Planning / Design Phase  
**Started:** 2026-02-03  
**Owner:** Aaron (chingchao.wu)  
**Lead Agent:** Kobot

## 📋 Project Overview

Building a multi-agent AI system for Rakuten Kobo, with specialized bots for different domains while maintaining clear separation of concerns and security boundaries.

## 🎯 Goals

1. **AskKobot** - Community support bot for Slack (#kobo-japan-ai-sharing)
2. **ClickUpBot** - Task/project management specialist
3. **MeetingBot** - Weekly townhall meeting summarization
4. **RHSBot** - HR/internal systems assistant
5. **Kobot** - Personal work assistant (this agent) coordinating the ecosystem

## 🗂️ Folder Structure

```
projects/multi-agent-kobo/
├── README.md                    # This file - project overview
├── ARCHITECTURE.md              # System architecture design
├── STRATEGY.md                  # Implementation strategy & roadmap
├── SECURITY.md                  # Security considerations & policies
├── discussions/                 # Design discussions & decisions
│   └── YYYY-MM-DD-topic.md
├── agents/                      # Agent specifications
│   ├── askkobot/
│   ├── clickupbot/
│   ├── meetingbot/
│   └── rhsbot/
├── infrastructure/              # Docker, deployment configs
│   ├── docker/
│   ├── deployment/
│   └── monitoring/
└── shared/                      # Shared resources
    ├── knowledge-base/          # Shared documentation
    └── coordination/            # Inter-agent protocols
```

## 📚 Key Documents

- [Architecture Design](./ARCHITECTURE.md) - System design and bot relationships
- [Implementation Strategy](./STRATEGY.md) - Phased rollout plan
- [Security Guidelines](./SECURITY.md) - Security boundaries and policies

## 🚦 Current Phase

**Phase 0: Planning** (Current)
- Defining architecture
- Gathering requirements
- Designing security model
- Creating Docker infrastructure

**Next:** Phase 1 - AskKobot MVP

## 📝 Meeting Notes

- [2026-02-03: Initial Discussion](./discussions/2026-02-03-initial-vision.md)

## 🔗 Related Resources

- OpenClaw docs: `/Users/chingchao.wu/.nvm/versions/node/v24.13.0/lib/node_modules/openclaw/docs`
- Main workspace: `/Users/chingchao.wu/clawd`
- Slack: `#kobo-japan-ai-sharing`, `#kobot-coordination`
