# AI Intel Desk: a one-person virtual company of AI bots

One human (the chairman), a team of specialized AI bots, and a CEO bot that coordinates them all. The bots monitor X, Telegram and paid Chinese communities around the clock and turn the noise into a short brief three times a day.

## The team

| Bot | Role |
|---|---|
| 帝俊 · CEO | Plans work, assigns tasks, does final review, reports to the chairman 3x daily |
| 英招 · X monitor | Collects posts on watched topics from X |
| 帝江 · TG monitor | Reads selected Telegram groups (read-only user API) |
| 陆吾 · Community monitor | Pulls new posts from paid Knowledge Planet (知识星球) communities |
| 东皇太一 · Intel reviewer | First-pass filtering and scoring before anything reaches the brief |
| 玄武 · Knowledge base | Archives reviewed intel into a searchable knowledge base |
| 白泽 · Dev engineer | Builds the dashboard, scrapers and data pipelines |
| 烛龙 · Sysadmin | Lightweight monitoring of cloud servers (changes need chairman approval) |
| Devin Bot · CTO (next hire) | Maintains the pipeline and ships fixes via PR |

## Pipeline

```
X API ─┐
TG user API (Telethon, read-only) ─┼─> raw records (local files) ─> stage 1 filter ─> stage 2 LLM review ─> brief / dashboard / KB
Community session (incremental) ─┘
```

- **No browser scraping.** Official or user APIs only.
- **Incremental pulls.** Each source keeps a cursor; only items newer than the last cursor are fetched.
- **Cron every 3 hours**, dedupe by content hash and URL, exponential backoff on HTTP 429.
- **Token budget:** collection and stage-1 filtering run locally with zero LLM tokens; only the reviewer spends tokens.

## Two-stage filtering

1. **Local, free:** dedupe, drop known spam/shill accounts, keyword and topic match, cross-source boost (the same story on X + TG + communities ranks higher).
2. **LLM reviewer:** scores novelty, source credibility and relevance to the watchlist; anything under the threshold never reaches the brief.

## When a schema breaks

Every source has a validator. A failed parse quarantines the batch instead of polluting the brief. The Devin Bot gets the failing sample, patches the parser, adds a regression test and opens a PR; the chairman just approves.

## Privacy

Paid community content stays local. Anything shown outside the machine is a summary of at most 120 characters. No credentials, IPs or keys live in this repo.
