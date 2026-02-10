# X (Twitter) Personal Analytics Skill

Personal X/Twitter analytics for [OpenClaw](https://openclaw.ai) — monitor your posts, engagement, mentions, and followers. Built for daily briefings and accountability.

## What It Does

- **Timeline**: Your recent posts with full engagement metrics (impressions, likes, RTs, replies, quotes, bookmarks)
- **Mentions**: Who's replying to or quoting you, with their follower count
- **Follower Tracking**: Daily follower count with delta tracking over time
- **Accountability**: Checks if you're spending too much time on X when you should be working
- **Cost Optimized**: Persistent local store + incremental fetching = minimal API costs (~$0.60-1.50/mo)

## Setup

### 1. Get X API Keys

1. Go to [developer.x.com](https://developer.x.com)
2. Create a project and app
3. Generate: API Key, API Secret, Access Token, Access Token Secret, Bearer Token

### 2. Install the Skill

```bash
# For OpenClaw
cd ~/.openclaw/workspace/skills
git clone https://github.com/aaronnev/x-twitter-skill.git x-twitter

# Requires uv (Python package runner)
# Install: curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 3. Run Setup

```bash
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_setup.py
```

The setup will:
- Ask for your API keys (or import from `~/.openclaw/.env`)
- Ask for your X handle
- Let you pick a budget tier (lite/standard/intense)
- Validate credentials
- Save config to `~/.openclaw/skills-config/x-twitter/config.json`

### 4. Test It

```bash
# Check your profile
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_user.py me

# Pull recent posts
uv run ~/.openclaw/workspace/skills/x-twitter/scripts/x_timeline.py recent --max 5
```

## Cost

X API v2 uses pay-per-use pricing:
- Tweet reads: ~$0.005 per request
- User reads: ~$0.01 per request

This skill minimizes costs with:
- **Persistent store**: tweets fetched once, stored forever locally
- **Incremental fetching**: `since_id` means only new tweets are fetched
- **Daily budget guard**: blocks requests when daily limit is hit
- **Budget tiers**: lite ($0.03/day), standard ($0.10/day), intense ($0.25/day)

Typical monthly cost: **$0.60 - $1.50** for daily briefings.

## Commands

| Command | What It Does | API Cost |
|---------|-------------|----------|
| `x_timeline.py recent` | Recent posts + engagement | ~$0.005 |
| `x_timeline.py top` | Top posts from local store | $0 |
| `x_timeline.py activity` | Accountability check | ~$0.005 |
| `x_timeline.py refresh ID` | Update one tweet's metrics | ~$0.005 |
| `x_mentions.py recent` | Recent mentions/replies | ~$0.005 |
| `x_mentions.py recent --context` | Mentions + parent tweets | ~$0.005-0.03 |
| `x_user.py me` | Your profile stats | ~$0.01 |
| `x_user.py me --track` | Profile + save follower delta | ~$0.01 |
| `x_user.py lookup USER` | Any user's profile | ~$0.01 |

## How It Works

### Architecture

The skill talks directly to the X API v2 using OAuth 1.0a (your API keys). No middlemen, no third-party proxies.

```
You ask OpenClaw → OpenClaw reads SKILL.md → runs the right script via uv
                                                      ↓
                                              Script hits X API v2
                                                      ↓
                                         Response stored locally (data/)
                                                      ↓
                                           Clean output → agent → you
```

### Cost Optimization (3 layers)

1. **Persistent local store** — Every tweet/mention gets saved to `data/tweets.json` on first fetch. Subsequent requests serve from local storage. A tweet is never re-fetched unless you explicitly ask (`refresh`).

2. **Incremental `since_id` fetching** — Each fetch stores the newest tweet ID. Next time, the API only returns tweets newer than that. First run pulls your recent history; after that, only new posts cost anything.

3. **Daily budget guard** — Tracks every API call in `data/usage.json`. If your daily spend hits the limit, the skill warns you and stops making calls (override with `--force`).

### Accountability Mode

`x_timeline.py activity` checks your recent posting activity and tells the agent:
- When you last posted
- How many posts today / this hour
- Whether you've been spending too much time on X

The agent can use this to nudge you back to work.

## File Structure

```
x-twitter/
├── SKILL.md              # OpenClaw skill manifest
├── README.md             # This file
├── .gitignore
├── scripts/
│   ├── x_setup.py        # Credential setup + validation
│   ├── x_timeline.py     # Posts + engagement + accountability
│   ├── x_mentions.py     # Mentions and replies
│   └── x_user.py         # Profile info + follower tracking
└── references/
    └── x-api-quickref.md # API endpoint reference

# Config (per-user, NOT in repo):
~/.openclaw/skills-config/x-twitter/
├── config.json           # Credentials + settings
└── data/
    ├── tweets.json       # Persistent tweet store
    ├── mentions.json     # Persistent mention store
    └── usage.json        # Daily API cost tracking
```

## Prerequisites

- [uv](https://astral.sh/uv) — Python package runner (handles dependencies automatically)
- X API developer account with credits loaded at [developer.x.com](https://developer.x.com)
- [OpenClaw](https://openclaw.ai) (or any agent platform that reads SKILL.md)

## Credits

Built by [@aaronnev_](https://x.com/aaronnev_) with [Claude Code](https://claude.ai/code) (Claude Opus 4.6).

Uses [tweepy](https://github.com/tweepy/tweepy) for X API v2 access and [uv](https://github.com/astral-sh/uv) for zero-config Python script execution.

## License

MIT
