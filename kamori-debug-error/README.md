# kamori-debug-error

A Claude skill for debugging specific errors using [Kamori](https://kamori.io) logs.

## What it does

Paste an error message, exception, or stack trace and Claude will find it in your production logs, determine how often it's happening, trace it back through your distributed system, and tell you whether it's a new regression or a pre-existing issue.

Steps:
1. **Find occurrences** — FTS5 search with key terms from the error
2. **Scope and frequency** — count in the last hour + hourly histogram
3. **Inspect the log** — full JSON body of the most recent match
4. **Distributed trace** — reconstruct the request chain if `trace_id` is present
5. **First occurrence** — was this introduced by a recent deploy?
6. **Blast radius** — error distribution across services

## Example prompts

```
I'm seeing this error: connection refused to redis:6379
```
```
Debug this exception: TypeError: Cannot read property 'id' of undefined
```
```
How often is ERR_PAYMENT_TIMEOUT happening?
```
```
Find log id 4291 and tell me what went wrong
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

## Installation

```bash
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-debug-error

# Or manually:
mkdir -p ~/.claude/skills/kamori-debug-error
curl -o ~/.claude/skills/kamori-debug-error/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-debug-error/SKILL.md
```
