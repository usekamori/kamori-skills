# kamori-digest

A Claude skill for generating log digests using [Kamori](https://kamori.io).

## What it does

Produces a human-readable health summary for a time period — daily or weekly. Useful for standups, end-of-day reviews, weekly reports, or sharing system status with non-technical stakeholders.

The digest includes:
1. **Service health table** — error/warn counts per service with a normal/elevated/anomalous signal
2. **Top error patterns** — the 10 most common error messages with occurrence counts
3. **Volume trend** — hourly (daily) or daily (weekly) log volume narrative
4. **Anomaly highlights** — services whose error rate is significantly above their 7-day baseline
5. **Notable events** — single events worth calling out (new error types, sudden bursts, recoveries)

The output is a prose narrative, not a raw data dump — written to be read in 2 minutes.

## Example prompts

```
Give me a daily digest
```
```
Weekly log summary
```
```
What happened in the last 24 hours?
```
```
System health report for this week
```
```
Summarize the logs for the standup
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

## Installation

```bash
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-digest

# Or manually:
mkdir -p ~/.claude/skills/kamori-digest
curl -o ~/.claude/skills/kamori-digest/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-digest/SKILL.md
```
