# kamori-deploy-check

A Claude skill for post-deploy health checks using [Kamori](https://kamori.io) logs.

## What it does

Run this immediately after deploying to production. Claude compares error rates and log patterns before and after your deployment timestamp and produces a clear verdict:

- **PASS** — error counts stable, no new patterns
- **WARN** — mild increase or new patterns at low volume
- **FAIL** — significant error spike, anomalous rate, or new critical errors

The check covers:
1. **Error histogram** — 5-minute buckets around the deploy window
2. **Before/after counts** — concrete numbers, not just percentages
3. **New error patterns** — FTS search for errors that appeared after the deploy
4. **Warning signals** — new warn-level logs that may precede failures
5. **Anomaly check** — spike factor vs 7-day baseline

## Example prompts

```
We just deployed the API service — run a deploy check
```
```
Deployment health check for payments, we deployed at 14:32 UTC
```
```
Did the deploy 20 minutes ago break anything?
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

## Installation

```bash
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-deploy-check

# Or manually:
mkdir -p ~/.claude/skills/kamori-deploy-check
curl -o ~/.claude/skills/kamori-deploy-check/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-deploy-check/SKILL.md
```
