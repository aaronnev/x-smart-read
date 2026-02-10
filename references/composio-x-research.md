# Composio X Research — Potential Companion Skill

> Reference notes for a future build. This skill (x-twitter) handles **personal analytics**. A Composio-based skill would handle **outward search/research** at zero API cost.

## What It Is

[Composio](https://composio.dev) is a platform that proxies API calls. Their free tier gives 20,000 calls/month. One of their integrations is Twitter/X — same search data, zero cost.

[@xBenJamminx](https://x.com/xBenJamminx) forked an X research skill to use Composio instead of direct X API:
- **Repo**: https://github.com/xBenJamminx/x-research-skill
- **Original**: https://github.com/rohunvora/x-research-skill (direct X API, paid)
- **Stack**: TypeScript, runs via `bun`

## What It Does

- Search tweets by keyword/topic (last 7 days)
- Sort by likes, impressions, retweets, or recency
- Follow full conversation threads
- Profile lookups (any user's recent activity)
- Watchlist — monitor specific accounts
- Cache (15-min TTL)
- Output formats: Telegram, markdown, JSON

## How It Differs From x-twitter

| | x-twitter (this skill) | x-research (Composio) |
|---|---|---|
| **Purpose** | Monitor YOUR account | Search/research across all of X |
| **Auth** | Your X API keys (OAuth 1.0a) | Composio API key |
| **Cost** | ~$0.02/day (pay-per-use) | $0 (free tier, 20K calls/mo) |
| **Data** | Your timeline, mentions, impressions | Any public tweets, threads, profiles |
| **Runtime** | Python + uv | TypeScript + bun |
| **Impression counts** | Yes (your tweets only) | No |
| **Follower tracking** | Yes | No |
| **Accountability** | Yes | No |
| **Topic search** | No | Yes |
| **Thread following** | No | Yes |

## Why Not Build It Yet

As of Feb 2026, there's a risk Composio hasn't updated for X's new pay-per-use model. The free proxy may break if X enforces billing on their end. Ben's post even hints at this being an arbitrage window.

Worth revisiting if:
- Composio confirms they've adapted to pay-per-use
- The free tier stays stable for a few months
- You want outward X research (trending topics, competitor monitoring, etc.)

## Install Notes (When Ready)

```bash
# Clone into OpenClaw skills
cd ~/.openclaw/workspace/skills
git clone https://github.com/xBenJamminx/x-research-skill.git x-research

# Install bun (if not present)
curl -fsSL https://bun.sh/install | bash

# Set Composio API key
# 1. Sign up at https://composio.dev
# 2. Connect Twitter through their dashboard
# 3. Add to env:
export COMPOSIO_API_KEY="your-key"
```

## Composio Twitter Actions Available

From their docs (https://docs.composio.dev/toolkits/twitter):

**Works:**
- Search recent tweets (`TWITTER_RECENT_SEARCH`)
- Look up user by ID/username
- Get tweet by ID
- Get followers/following
- Home timeline (feed)

**Missing:**
- User tweet timeline (YOUR tweets) — use `from:username` search workaround
- User mentions endpoint — use `to:username` search workaround
- `get_me` (authenticated user) — use username lookup instead
- Impression counts — not available through search

## Two-Skill Architecture

If both are installed, the agent uses them for different things:

```
User: "What are people saying about AI agents?"
→ x-research skill (Composio, free search)

User: "How are my posts doing today?"
→ x-twitter skill (direct API, personal analytics)

User: "Check my mentions and also search what's trending in crypto"
→ x-twitter for mentions + x-research for search
```

The SKILL.md triggers are designed to not overlap — x-twitter triggers on "my posts", "my mentions", "my followers". x-research would trigger on "search X for", "what are people saying about", "check what @someone posted".
