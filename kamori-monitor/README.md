# kamori-monitor

A Claude skill for live log monitoring using [Kamori](https://kamori.io).

## What it does

Starts a real-time log monitoring session — the equivalent of `tail -f` for your production logs, with anomaly awareness and error alerting built in.

How it works:
1. **Baseline** — records current error rate and anomaly signal before monitoring starts
2. **Anchor** — finds the current log tail position so you only see new events
3. **Live watch** — loops `watch_logs` (long-polling, up to 30s per call) for new entries
4. **Alerting** — calls out error bursts, new anomalies, and stack traces as they arrive
5. **Session summary** — reports total events and final anomaly signal when you stop

Claude formats each new log line cleanly and alerts prominently when something needs attention.

## Example prompts

```
Watch the auth service for errors
```
```
Monitor logs while I deploy
```
```
Live tail the payments service — let me know if anything goes wrong
```
```
Keep an eye on all services for the next 10 minutes
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

## Installation

```bash
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-monitor

# Or manually:
mkdir -p ~/.claude/skills/kamori-monitor
curl -o ~/.claude/skills/kamori-monitor/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-monitor/SKILL.md
```
