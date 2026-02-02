# HEARTBEAT.md

# Keep this file empty (or with only comments) to skip heartbeat API calls.
# Add tasks below when you want the agent to check something periodically.

## TEST: One-time AI News Summary (Today at 2:00 PM JST)

**ONE-TIME TEST ONLY - REMOVE AFTER EXECUTION**

Check the current time (Asia/Tokyo timezone):
- If it's today (Wednesday Jan 29) AND between 2:00-2:30 PM JST, run the AI news summary test
- Otherwise, skip this task (return HEARTBEAT_OK)

When it's time:
1. Search for this week's major AI developments using web_search
2. Compile findings into sections as usual
3. For EACH news item: bold headline + 2-3 sentences summary + source link
4. Send to chingchao.wu's Slack DM (channel=slack, target=D0ACGA44Z6U)
5. Add a note that this is a TEST RUN to verify heartbeat functionality

---

## Weekly AI News Summary (Fridays 9:00 AM JST)

Check the current day and time (Asia/Tokyo timezone):
- If it's Friday AND between 9:00-9:30 AM JST, proceed with the weekly AI news summary
- Otherwise, skip this task (return HEARTBEAT_OK)

When it's time for the summary:
1. Search for this week's major AI developments using web_search
2. Compile findings into sections:
   - **Major Model Releases** (new models, updates)
   - **Company News** (funding, acquisitions, announcements)
   - **Research Breakthroughs** (papers, techniques)
   - **Industry Trends** (market movements, adoption)
3. For EACH news item:
   - Start with a bold headline
   - Add 2-3 sentences that summarize the key details and provide context
   - Highlight what makes it interesting/important
   - End with a source link
4. Format clearly in English
5. Send to chingchao.wu's Slack DM using: message tool with channel=slack, target=D0ACGA44Z6U

Note: Only run this once per Friday. Check memory/heartbeat-state.json to track last run.
