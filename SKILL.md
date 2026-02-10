---
name: x-twitter
description: >
  Personal X (Twitter) analytics — timeline engagement, mentions, follower tracking,
  and accountability checks via X API v2. Use for morning briefings, performance reviews,
  and keeping the user focused. Cost-optimized with persistent local store and daily budget guards.
metadata: {"openclaw":{"emoji":"𝕏","requires":{"bins":["uv"]}}}
---

# X (Twitter) Personal Analytics

Monitor your X account — posts, engagement, mentions, followers. Built for daily briefings and accountability.

## Triggers

Use this skill when the user asks about:
- Their X / Twitter posts, timeline, or engagement
- Mentions, replies, or who's talking about them on X
- Follower count, profile stats, follower growth
- "What's happening on my X?" / "How are my posts doing?"
- "Check my Twitter mentions" / "Any new replies?"
- Morning briefing / daily social media summary
- "Am I on X too much?" / accountability check
- X/Twitter analytics or performance

NOT for: searching X for topics (use x-research skill), posting tweets, account management.

## Prerequisites

Run setup first (imports credentials from `~/.openclaw/.env` or prompts interactively):
```bash
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_setup.py
```

## Commands

### Timeline — your posts + engagement

```bash
# Recent posts with engagement metrics
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py recent

# Last 5 posts
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py recent --max 5

# Posts from last 24 hours
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py recent --hours 24

# Top posts by engagement (from local store, no API call)
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py top --days 7

# Refresh metrics for a specific tweet
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py refresh TWEET_ID

# Accountability check — are they on X right now?
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py activity
```

### Mentions — who's talking to/about you

```bash
# Recent mentions
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_mentions.py recent

# Mentions from last 24 hours
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_mentions.py recent --hours 24

# Mentions with context (shows what they replied to — costs extra)
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_mentions.py recent --context
```

### User Profile — stats + follower tracking

```bash
# Your profile stats
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_user.py me

# Track follower changes over time
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_user.py me --track

# Look up another user
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_user.py lookup someuser
```

### Setup

```bash
# Validate credentials
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_setup.py --check

# Show config (secrets redacted)
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_setup.py --show
```

## Workflows

### Morning Brief
```bash
uv run x_timeline.py recent --hours 24
uv run x_mentions.py recent --hours 24
uv run x_user.py me --track
```

### Accountability Check
```bash
uv run x_timeline.py activity
```
Use this when the user should be working — it shows when they last posted and how active they've been. Nudge them if they're spending too much time on X.

### Weekly Review
```bash
uv run x_timeline.py top --days 7
uv run x_user.py me --track
```

## Cost Notes

X API v2 pay-per-use pricing:
- Tweet reads: ~$0.005 per request (up to 100 tweets)
- User reads: ~$0.01 per request

The skill minimizes costs with:
- **Persistent local store**: fetched tweets are stored forever, never re-fetched
- **`since_id` incremental fetching**: only gets new tweets since last check
- **Daily budget guard**: warns and blocks if daily spend limit is exceeded
- **Mentions as notifications**: new mentions signal engagement without re-checking every post

Typical costs:
- Daily briefing: ~$0.02/day ($0.60/mo)
- With 5 checks/day: ~$0.03/day ($0.90/mo)
- Budget tiers: lite ($0.03/day), standard ($0.10/day), intense ($0.25/day)

Every command prints its API cost and today's running total.
