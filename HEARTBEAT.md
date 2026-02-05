# HEARTBEAT.md

# Keep this file empty (or with only comments) to skip heartbeat API calls.
# Add tasks below when you want the agent to check something periodically.

## Weekly System Update Check (Mondays 1:00 PM JST)

Check the current day and time (Asia/Tokyo timezone):
- If it's Monday AND between 1:00-1:30 PM JST, proceed with update check
- Otherwise, skip this task

When it's time:
1. Check Node.js version vs OpenClaw installation location
2. Check for new OpenClaw releases on npm
3. Check for Node.js LTS updates
4. Assess risk level (critical/medium/low) based on:
   - Security vulnerabilities
   - Breaking changes in changelog
   - Version type (major/minor/patch)
5. Send report to Aaron's Slack DM (channel=slack, target=D0ACGA44Z6U) with:
   - Current versions
   - Available updates
   - Risk assessment
   - Recommendation (upgrade now / wait / skip)
   - Link to changelogs

Note: Only check once per week. Track in memory/heartbeat-state.json.

---

## Weekly AI News Summary (Fridays 9:00 AM JST)

Check the current day and time (Asia/Tokyo timezone):
- If it's Friday AND between 9:00-9:30 AM JST, proceed with the weekly AI news summary
- Otherwise, skip this task (return HEARTBEAT_OK)

When it's time for the summary:
1. Search for this week's major AI developments using web_search
2. **Select 2-3 most interesting/relevant topics** (quality over quantity)
   - Prioritize: Impact on product/engineering, practical applications, major breakthroughs
   - Skip: Minor updates, redundant news, purely academic papers without practical impact
3. For EACH selected topic:
   - **Catchy headline** (one line, engaging)
   - **Why it matters** (2-3 sentences max - focus on "so what?")
   - **Key takeaway** (one practical insight or implication)
   - **Source link** for those who want to dive deeper
4. **Keep it short:** Target 5-minute read (approx 300-400 words total)
5. **Add a closing thought:** One sentence tying topics together or looking ahead
6. Format clearly in English with emoji for visual appeal
7. **Save to file:** `ai-news/YYYY-MM-DD-weekly-ai-news.md` (create ai-news/ if needed)
8. **Send to #kobo-japan-ai-sharing:** channel=slack, target=C09AWDW6GDA
9. **Commit to Git:** Add, commit, and push the ai-news file to GitHub

**Tone:** Conversational, insightful, practical. Like sharing interesting findings with colleagues over coffee.

Note: Only run this once per Friday. Check memory/heartbeat-state.json to track last run.
