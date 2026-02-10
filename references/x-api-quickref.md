# X API v2 Quick Reference

## Endpoints Used

| Endpoint | Route | Auth | Cost |
|----------|-------|------|------|
| Get Me | `GET /2/users/me` | OAuth 1.0a | ~$0.01 |
| User Tweets | `GET /2/users/:id/tweets` | OAuth 1.0a | ~$0.005 |
| User Mentions | `GET /2/users/:id/mentions` | OAuth 1.0a | ~$0.005 |
| Get User by Username | `GET /2/users/by/username/:username` | Bearer | ~$0.01 |
| Get Tweet | `GET /2/tweets/:id` | Bearer/OAuth | ~$0.005 |

## Tweet Fields

Request via `tweet.fields` parameter:

| Field | Description |
|-------|-------------|
| `created_at` | Tweet creation timestamp (ISO 8601) |
| `text` | Full tweet text |
| `public_metrics` | Engagement counters (see below) |
| `conversation_id` | Root tweet ID of the conversation |
| `in_reply_to_user_id` | User ID being replied to |
| `referenced_tweets` | Array of {type, id} — "replied_to", "quoted", "retweeted" |
| `author_id` | Author's user ID |

## Public Metrics (tweet)

Available in `public_metrics` when requested:

| Metric | Description | Notes |
|--------|-------------|-------|
| `retweet_count` | Number of retweets | Public |
| `reply_count` | Number of replies | Public |
| `like_count` | Number of likes | Public |
| `quote_count` | Number of quote tweets | Public |
| `bookmark_count` | Number of bookmarks | Public |
| `impression_count` | Number of views | Own tweets only (OAuth 1.0a required) |

## User Fields

Request via `user.fields` parameter:

| Field | Description |
|-------|-------------|
| `created_at` | Account creation date |
| `description` | Bio text |
| `location` | Location string |
| `public_metrics` | followers_count, following_count, tweet_count, listed_count |
| `profile_image_url` | Avatar URL |
| `url` | Profile link |
| `verified` | Blue checkmark |
| `verified_type` | "blue", "business", "government", or none |

## Rate Limits

Per-app rate limits (15-minute windows):

| Endpoint | Rate Limit |
|----------|-----------|
| User Tweets | 900 req/15min |
| User Mentions | 450 req/15min |
| Users Lookup | 300 req/15min |
| Tweet Lookup | 300 req/15min |

tweepy's `wait_on_rate_limit=True` auto-handles 429 responses.

## Pay-Per-Use Pricing (2026)

| Operation | Cost |
|-----------|------|
| Post reads (tweets) | $0.005/request |
| User reads | $0.01/request |
| DM reads | $0.01/request |

Note: Each request can return up to 100 tweets (via `max_results`).

## Search Operators (for reference)

Useful if combining with search endpoints:
- `from:username` — tweets by a user
- `to:username` — replies to a user
- `@username` — mentions of a user
- `conversation_id:ID` — all tweets in a thread
- `-is:retweet` — exclude retweets
- `has:links` — tweets with URLs
- `has:media` — tweets with images/video

## Auth Methods

| Method | When | Keys Needed |
|--------|------|-------------|
| OAuth 1.0a (User Context) | Own tweets, mentions, get_me | API Key + Secret + Access Token + Secret |
| Bearer Token (App Context) | Public user lookup, public tweets | Bearer Token only |

OAuth 1.0a is required for `impression_count` on your own tweets.
